<!-- refreshed: 2026-11-02 -->
# Architecture

**Analysis Date:** 2026-11-02

## System Overview

The checkout is an EROS multi-repository workspace, not an application server. Implemented build and delivery infrastructure lives in `forge/`; `agents/README.md` and `documents/README.md` are repository placeholders. Do not infer robot middleware, service APIs, or application execution architecture from the EROS name.

```text
Host entry / consuming Bazel module
  forge/forge-bazel       forge/forge-shell
             \             /
          forge/forge_container.py
        locked persistent Docker container
                     |
   forge/MODULE.bazel + forge/bazel/bazelrc
                     |
   environment capture + normalized variants
   forge/bazel/toolchain/   forge/bazel/config/
                     |
       +-------------+--------------------+
       |             |                    |
 Native C++       Foreign CMake       Locked Conan+CMake
 runtime.bzl     cmake_forge.bzl     cmake_conan_forge.bzl
       |             |                    |
       +-------- CcInfo / install / debug providers -----+
                            |
                runtime closure aspect
                forge/bazel/runtime.bzl
                            |
          forge/bazel/deb.bzl -> bazel/packaging/driver.py
                            |
           tar / deb / dbgsym / manifests / test payload
```

Diagram rule filenames are relative to `forge/bazel/`; execution belongs to the controlled build environment, not the host toolchain.

## Component Responsibilities

| Component | Responsibility | File |
|-----------|----------------|------|
| Host transport | Discover consuming module; validate image, mounts and state; forward commands/signals | `forge/forge_container.py` |
| Environment verification | Compare actual controlled tools against the reviewed local image contract | `forge/docker/verify_environment.py` |
| Module composition | Register external dependencies, tool repository and C++ toolchains | `forge/MODULE.bazel` |
| Environment repository | Capture tools and watch their identity-bearing files/trees | `forge/bazel/toolchain/host_tools.bzl` |
| Variant policy | Normalize platform, compilation mode and sanitizer semantics | `forge/bazel/config/validation.bzl` |
| Native adapters | Publish native ELF/debug outputs and runtime mappings | `forge/bazel/runtime.bzl` |
| Foreign CMake adapter | Run rules_foreign_cc and adapt its install tree to strict exports | `forge/bazel/cmake_forge.bzl` |
| Conan/CMake rule | Declare action inputs/outputs and expose compile/link/install/debug interfaces | `forge/bazel/cmake_conan_forge.bzl` |
| Execution drivers | Acquire pinned dependencies, build/install, verify and emit provenance | `forge/bazel/tools/cmake_action.py`, `forge/bazel/tools/conan_action.py` |
| Packaging analysis | Compute runtime/package dependencies and declare packaging actions | `forge/bazel/deb.bzl` |
| Packaging execution | Inspect ELF closure, enforce ownership, assemble runtime and symbols | `forge/bazel/packaging/driver.py` |
| Acceptance orchestration | Run registered checks and preserve separate result states/evidence | `forge/tests/test_runner.py`, `forge/tests/acceptance_cases.py` |

## Pattern Overview

**Overall:** Declarative build graph with analysis-time adapters and execution-time command drivers.

**Key Characteristics:**
- Keep dependency orchestration in Bazel; keep package-internal builds in CMake and third-party acquisition in Conan (`forge/AGENTS.md`).
- Pass serializable action specifications from Starlark to Python, rather than discovering undeclared inputs during execution (`forge/bazel/cmake_conan_forge.bzl:28`, `forge/bazel/deb.bzl:11`).
- Carry semantic metadata through providers, not inferred output filenames alone (`forge/bazel/providers.bzl`, `forge/bazel/runtime.bzl`).
- Treat compiled files, runtime closures, symbol artifacts and provenance manifests as distinct outputs (`forge/bazel/cmake_conan_forge.bzl:202`).
- Separate implementation from acceptance claims: remote image publication, ARM64-host support and target deployment acceptance are not established merely by recipes or cross-build rules (`forge/README.md`, `forge/documents/technical-design.md`).

## Layers

**Host/container boundary:**
- Purpose: Provide one reviewed Linux/amd64 execution environment per source workspace.
- Location: `forge/forge_container.py`, `forge/docker/exec_command.py`.
- Contains: Image/state verification, Docker execution, source/Git metadata mounts and signal forwarding.
- Depends on: Host Python, local Docker, `forge/docker/image.lock.json`.
- Used by: `forge/forge-bazel`, `forge/forge-shell`.

**Configuration and tool identity:**
- Purpose: Make execution tools, target selection and feature policy explicit graph inputs.
- Location: `forge/bazel/toolchain/`, `forge/bazel/config/`, `forge/bazel/bazelrc`.
- Contains: Repository extension, environment manifests, C++ toolchain definitions, CMake toolchains and Conan profiles.
- Depends on: Controlled installed tools and `forge/bazel/toolchain/environment_spec.json`.
- Used by: Native, foreign-build and packaging rules.

**Build adapters:**
- Purpose: Present a common Bazel compile/link and install interface over different build systems.
- Location: `forge/bazel/runtime.bzl`, `forge/bazel/cmake_forge.bzl`, `forge/bazel/cmake_conan_forge.bzl`.
- Contains: Native macros, foreign_cc overlay, strict export descriptors and declared driver actions.
- Depends on: rules_cc, rules_foreign_cc, variant validation and tool identities.
- Used by: Consumer `BUILD.bazel` targets such as `forge/examples/02-native-library/BUILD.bazel` and `forge/examples/04-conan-locked/BUILD.bazel`.

**Execution and publication:**
- Purpose: Materialize declared installs and validated distribution artifacts.
- Location: `forge/bazel/tools/`, `forge/bazel/packaging/driver.py`.
- Contains: Isolated build homes, pinned acquisition, ELF inspection, deterministic archive construction and metadata checks.
- Depends on: JSON action specifications and declared tool/environment inputs.
- Used by: Starlark actions in `forge/bazel/cmake_conan_forge.bzl`, `forge/bazel/runtime.bzl`, `forge/bazel/deb.bzl`.

## Data Flow

### Primary Request Path

1. Host command delegates to `main("bazel", sys.argv[1:])` (`forge/forge-bazel:10`).
2. Locate nearest ancestor `MODULE.bazel`, verify local locked image, compute state key from workspace/image/user/instance, and acquire startup locks (`forge/forge_container.py:32`, `forge/forge_container.py:111`).
3. Create or validate the persistent container, verify its actual environment, check that Bazel output state remains under the instance directory, and execute the requested command (`forge/forge_container.py:148`, `forge/forge_container.py:194`).
4. Bazel registers the environment extension and toolchains; the extension captures and watches actual tool identities (`forge/MODULE.bazel:12`, `forge/bazel/toolchain/host_tools.bzl:3`).
5. Rules normalize variants, serialize configuration and declare outputs before execution (`forge/bazel/config/validation.bzl:26`, `forge/bazel/cmake_conan_forge.bzl:55`).
6. The action driver builds or adapts an install tree, validates exact exports, emits debug/source artifacts and verifies private-home removal (`forge/bazel/tools/cmake_action.py:16`).
7. Providers expose the results to C++ consumers and packaging targets (`forge/bazel/cmake_conan_forge.bzl:202`).

### Two CMake Build Paths

1. `cmake_forge` creates a target-configured toolchain overlay and private foreign_cc build target, then passes its install tree through `cmake_build` (`forge/bazel/cmake_forge.bzl:53`).
2. `cmake_conan_forge` uses the combined action path; `cmake_action.build` invokes `conan_action.prepare` inside its temporary work directory before invoking CMake (`forge/bazel/tools/cmake_action.py:27`).
3. The Conan path consumes the verified original dependency packages during that same action, copies host-context package content to the final install tree, and excludes build-context tools from that copying step (`forge/bazel/tools/cmake_action.py:110`).
4. Both routes validate declared output types and expose the same providers; do not model the foreign_cc route as the same build action as the combined Conan route (`forge/bazel/cmake_forge.bzl:109`, `forge/bazel/cmake_conan_forge.bzl:202`).

### Runtime and Debian Publication

1. Traverse `deps`, `data` and `target` with `runtime_closure`; preserve explicit mappings and per-file ownership (`forge/bazel/runtime.bzl:54`).
2. Select actual runfiles rather than every shared alternative in `CcInfo` (`forge/bazel/runtime.bzl:126`).
3. Serialize mappings, manifests, explicit owned targets and checked external Debian dependencies (`forge/bazel/deb.bzl:47`).
4. Collect entries, resolve links, prune runtime closure, validate ELF/architecture and enforce publication ownership (`forge/bazel/packaging/driver.py`).
5. Produce private `/opt/eros/<package>` payloads and paired debug metadata; expose archive/package identities through `ErosPackageInfo` and `ErosDebRuntimeInfo` (`forge/bazel/deb.bzl`).
6. Multiarch packaging uses a split configuration transition rather than relabeling one artifact (`forge/bazel/deb.bzl:307`).

**State Management:**
- Persistent workspace state lives outside source under `~/.cache/eros-forge/<key>`; the launcher derives container HOME/TMPDIR from that state (`forge/forge_container.py:128`).
- Conan+CMake action state is temporary and action-local, including HOME/CONAN_HOME; the driver confirms removal before writing the final manifest (`forge/bazel/tools/cmake_action.py:177`).
- Bazel graph/cache state is separate from final runtime outputs; `no-remote-exec` restricts execution, not all cache reuse (`forge/bazel/cmake_conan_forge.bzl:45`, `forge/README.md`).
- Acceptance state consists of command logs and JSON/result records rather than application database state (`forge/tests/test_runner.py`).

## Key Abstractions

**Strict export descriptor:**
- Purpose: Declare exact install-relative public files and logical link alternatives.
- Examples: `forge/bazel/cmake_conan_forge.bzl:55`, `forge/bazel/cmake_forge.bzl:71`.
- Pattern: Analysis-time dictionary converted to JSON; execution verifies all declared outputs.

```python
# Descriptor shape accepted by forge/bazel/cmake_conan_forge.bzl.
exports = {
    "headers": ["include/math/answer.h"],
    "includes": ["include"],
    "libraries": [{"name": "math", "static": "lib/libmath.a"}],
    "binaries": [],
    "runtime_files": [],
}
```

**Provider contracts:**
- `CcInfo` is the compile/link interface; consumers depend on targets rather than manually reaching into install trees (`forge/bazel/cmake_conan_forge.bzl:166`).
- `ErosInstallInfo` contains entries plus provenance manifests; entries retain owning target labels and optional ownership manifests (`forge/bazel/providers.bzl:3`).
- `ErosDebugInfo` carries transitive symbol artifacts and identity manifests (`forge/bazel/providers.bzl:11`).
- `RuntimeClosureInfo` aggregates runtime/debug closure over graph edges (`forge/bazel/runtime.bzl:12`).
- `ErosDebRuntimeInfo` carries checked runtime deb, payload, manifest, dbgsym and dependency closure (`forge/bazel/deb.bzl:9`).

**Normalized variant:**
- Purpose: Keep execution CPU, target CPU, Debug/Release and sanitizer settings consistent across native/CMake/Conan paths.
- Examples: `forge/bazel/config/validation.bzl`, `forge/bazel/config/variants.bzl`.
- Pattern: Shared validation returns a struct; generators supply matching toolchain/profile definitions.

## Entry Points

**Developer build and shell:**
- Location: `forge/forge-bazel`, `forge/forge-shell`.
- Triggers: Explicit developer commands from a Bazel module directory.
- Responsibilities: Enter the controlled environment without replacing tool policy with host tools.

**Consumer build API:**
- Location: `forge/bazel/runtime.bzl`, `forge/bazel/cmake_forge.bzl`, `forge/bazel/cmake_conan_forge.bzl`, `forge/bazel/deb.bzl`.
- Triggers: Bazel loads from consumer `BUILD.bazel` files.
- Responsibilities: Offer native, CMake, Conan and packaging interfaces; see `forge/examples/MODULE.bazel` for a separate consumer module.

**Maintenance and verification:**
- Location: `forge/bazel/toolchain/generate_files.py`, `forge/bazel/tools/conan_lock_update.py`, `forge/bazel/toolchain/export_runtime.sh`, `forge/tests/test_runner.py`.
- Triggers: Explicit generator checks, lock maintenance, runtime export or acceptance runs.
- Responsibilities: Keep maintenance separate from ordinary builds; runtime export is not a complete SDK or deployment acceptance (`forge/README.md`).

## Architectural Constraints

- **Threading/concurrency:** Python drivers orchestrate synchronous subprocesses; Bazel/CMake provide build parallelism. Startup uses filesystem locks and rejects another active workspace instance; use separate checkouts for independent containers (`forge/forge_container.py:148`, `forge/bazel/tools/cmake_action.py:105`).
- **Global state:** Module-level ROOT/LOCK/PASSTHROUGH constants describe launcher policy, not a service singleton. Persistent mutable state belongs to Docker/Bazel instance storage (`forge/forge_container.py:15`).
- **Circular imports:** No architectural import cycle is established by the inspected core rule/driver files. `cmake_action.py` loads `conan_action` only on the combined path; shared helpers belong in `forge/bazel/tools/action_common.py`.
- **Tool policy:** Fail rather than fall back to host tools, auto-pull images or silently rewrite locks (`forge/forge_container.py`, `forge/AGENTS.md`).
- **Platform boundary:** Local launchers explicitly require the Linux/amd64 baseline. Four registered toolchains do not establish four validated host environments (`forge/forge_container.py:117`, `forge/MODULE.bazel:15`).
- **Analysis/execution boundary:** Keep Starlark interfaces in `forge/bazel/*.bzl` and execution code in `forge/bazel/tools/` or `forge/bazel/packaging/` (`forge/AGENTS.md:40`).
- **Generated configuration:** Change generator inputs/generator and check generated outputs together; do not hand-edit only one profile/toolchain (`forge/bazel/toolchain/generate_files.py`, `forge/AGENTS.md:46`).

## Anti-Patterns

### Inferring Runtime Delivery from Link Metadata Alone

**What happens:** `CcInfo` can offer static/shared alternatives that do not all appear in actual runtime selection; raw runfiles can also lose explicit ownership/mapping priority (`forge/bazel/runtime.bzl:31`, `forge/bazel/runtime.bzl:126`).
**Why it's wrong:** Packaging every alternative or discarding mapping ownership produces an incorrect delivery closure.
**Do this instead:** Use `runtime_closure`, `selected_runtime_entries`, explicit install mappings and checked Debian dependencies (`forge/bazel/runtime.bzl`, `forge/bazel/deb.bzl`). This is a guarded failure mode, not a claim that current rules bypass those guards.

### Encoding Target Policy in Ad Hoc Flags

**What happens:** Raw sanitizer/sysroot/loader flags can disagree across Bazel, CMake and Conan (`forge/bazel/config/validation.bzl:40`).
**Why it's wrong:** Dependency ABI and runtime policy diverge even if individual commands compile.
**Do this instead:** Use canonical configs and generated toolchain/profile selections; preserve the explicit rejections in `forge/bazel/config/validation.bzl` and `forge/bazel/tools/cmake_action.py:64`.

## Error Handling

**Strategy:** Reject invalid configuration early and fail closed on environment, output, provenance or runtime mismatches.

**Patterns:**
- Starlark `fail(...)` rejects invalid exports, unsupported variants and contradictory flags before actions run (`forge/bazel/cmake_conan_forge.bzl`, `forge/bazel/config/validation.bzl`).
- Python drivers raise contextual exceptions and convert top-level failures to nonzero exits (`forge/bazel/tools/cmake_action.py:197`, `forge/bazel/packaging/driver.py:1354`).
- The launcher converts Docker/environment failures to user-facing failures rather than fallback execution (`forge/forge_container.py`).
- Acceptance explicitly separates `PASS`, `FAIL`, `SKIP` and `BLOCKED`; missing infrastructure is not a product pass (`forge/tests/test_runner.py:25`).

## Cross-Cutting Concerns

**Logging:** Subprocess diagnostics and retained acceptance command logs; no web-service logging stack is present in the inspected implementation (`forge/tests/test_runner.py:92`, `forge/bazel/tools/action_common.py`).
**Validation:** Tool/environment digests, exact export types, safe relative paths, ELF identities, ownership and debug manifests span every publication boundary (`forge/bazel/toolchain/environment.py`, `forge/bazel/packaging/driver.py`).
**Authentication:** No application-user authentication layer is detected. Docker access and declared dependency acquisition are infrastructure concerns; never embed transport credentials in specs, manifests or evidence (`forge/README.md`, `forge/AGENTS.md`).

---

*Architecture analysis: 2026-11-02*
