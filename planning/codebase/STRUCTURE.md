# Codebase Structure

**Analysis Date:** 2026-11-02

## Directory Layout

```text
/home/user/programs/eros-ws/eros/
├── .repo/                         # repo workspace metadata; not product source
├── .git/                          # root Git metadata
├── .kilo/                         # local extension state, not build implementation
├── .vscode/                       # local editor configuration
├── .planning -> ./agents/planning/
├── AGENTS.md -> ./agents/AGENTS.md # broken root instruction link
├── agents/                        # independent agent/planning repository
│   ├── README.md                  # repository placeholder
│   └── planning/codebase/         # generated codebase maps
├── documents/                     # independent top-level documentation repository
│   └── README.md                  # repository placeholder
└── forge/                         # implemented build and delivery subproject
    ├── AGENTS.md                  # applicable Forge contribution rules
    ├── README.md                  # public setup and interface overview
    ├── forge-bazel                # host Bazel launcher
    ├── forge-shell                # host interactive-container launcher
    ├── forge_container.py         # shared host transport/state policy
    ├── MODULE.bazel               # eros_forge module and toolchain registrations
    ├── MODULE.bazel.lock          # module resolution lock
    ├── Dockerfile.amd64           # local supported-platform image recipe
    ├── Dockerfile.arm64           # candidate ARM64 recipe, not validated support
    ├── docker/                    # image identity, verification and dependencies
    ├── bazel/
    │   ├── BUILD.bazel            # exported Starlark/build assets
    │   ├── bazelrc                # shared configuration presets
    │   ├── providers.bzl          # install/debug interfaces
    │   ├── runtime.bzl            # native adapters and runtime aspect
    │   ├── cmake_forge.bzl        # foreign_cc adapter
    │   ├── cmake_conan_forge.bzl  # combined-action rule and strict exports
    │   ├── deb.bzl                # tar/deb/debug/test packaging interfaces
    │   ├── config/               # build settings, variant selection/validation
    │   ├── toolchain/            # controlled tools, environment and generators
    │   │   ├── cmake/            # generated preset CMake toolchains
    │   │   └── conan/            # generated build/host Conan profiles
    │   ├── tools/                # Python build/acquisition drivers
    │   └── packaging/            # Python publication driver and specialized tests
    ├── documents/                # actual Forge requirements/design/API docs
    ├── examples/                 # separate consuming Bazel module
    │   ├── MODULE.bazel
    │   ├── .bazelrc
    │   ├── _support/             # shared example Conan recipes and source
    │   ├── 01-minimal-app/       # native introductory consumer
    │   ├── 02-native-library/
    │   ├── 03-cmake-library/
    │   ├── 04-conan-locked/
    │   ├── 04b-cmake-protobuf/
    │   ├── 05-private-runtime/
    │   ├── 06-debian-package/
    │   ├── 06b-deb-dependency/
    │   ├── 07-release-symbols/
    │   ├── 08-sanitizer-errors/
    │   ├── 09-cross-build/
    │   └── check.py              # common example checker
    └── tests/
        ├── test_runner.py        # acceptance orchestration
        ├── acceptance_cases.py   # acceptance implementations/registration
        ├── test_*_unit.py        # Python unit regressions
        ├── test_container_entry.py
        └── fixtures/             # build graphs and intentional failure inputs
```

The top-level directories `agents/`, `documents/` and `forge/` contain separate Git metadata. Treat `/home/user/programs/eros-ws/eros` as the workspace navigation root, not the Bazel module root; the module roots are `forge/` and `forge/examples/` (`forge/MODULE.bazel`, `forge/examples/MODULE.bazel`).

## Directory Purposes

**`forge/`:**
- Purpose: Own the implemented build/delivery product rather than the entire robot operating system.
- Contains: Host launchers, module composition, Docker recipes and subsystem directories.
- Key files: `forge/AGENTS.md`, `forge/README.md`, `forge/forge_container.py`, `forge/MODULE.bazel`.

**`forge/bazel/`:**
- Purpose: Expose public Bazel interfaces and analysis-time graph adaptation.
- Contains: Starlark rules, providers, runtime aspect and packaging macros.
- Key files: `forge/bazel/runtime.bzl`, `forge/bazel/cmake_conan_forge.bzl`, `forge/bazel/deb.bzl`.
- Keep shell commands and filesystem-heavy execution in drivers, not in rule-interface code (`forge/AGENTS.md`).

**`forge/bazel/config/`:**
- Purpose: Share normalized platform/mode/sanitizer choices across build paths.
- Contains: Settings, selects and validation.
- Key files: `forge/bazel/config/settings.bzl`, `forge/bazel/config/variants.bzl`, `forge/bazel/config/validation.bzl`, `forge/bazel/config/BUILD.bazel`.

**`forge/bazel/toolchain/`:**
- Purpose: Describe actual controlled tool identities and matching build-system configuration.
- Contains: Environment capture/validation, C++ toolchain definitions, configuration generator, generated CMake files and Conan profiles.
- Key files: `forge/bazel/toolchain/environment.py`, `forge/bazel/toolchain/environment_spec.json`, `forge/bazel/toolchain/host_tools.bzl`, `forge/bazel/toolchain/generate_files.py`, `forge/bazel/toolchain/export_runtime.sh`.
- Generated examples: `forge/bazel/toolchain/cmake/linux_x86_64_cross_arm64.cmake`, `forge/bazel/toolchain/conan/linux_x86_64_cross_arm64_debug.profile`, `forge/bazel/toolchain/toolchain_data.bzl`.

**`forge/bazel/tools/`:**
- Purpose: Execute build/acquisition work from declared action configurations.
- Contains: CMake driver, Conan preparation/verification, deployer, explicit lock updater and common helpers.
- Key files: `forge/bazel/tools/cmake_action.py`, `forge/bazel/tools/conan_action.py`, `forge/bazel/tools/action_common.py`, `forge/bazel/tools/conan_lock_update.py`.

**`forge/bazel/packaging/`:**
- Purpose: Own runtime archive, Debian, ELF closure and debug-symbol publication logic.
- Contains: CLI/action driver and specialized regression tests.
- Key files: `forge/bazel/packaging/driver.py`, `forge/bazel/packaging/driver_test.py`.

**`forge/docker/`:**
- Purpose: Maintain reviewed image identity and container-side verification/transport support.
- Contains: Image lock, hashed Python dependency locks, APT source files and helper scripts.
- Key files: `forge/docker/image.lock.json`, `forge/docker/verify_environment.py`, `forge/docker/exec_command.py`, `forge/docker/requirements.in`.
- Do not interpret the presence of `forge/Dockerfile.arm64` as a supported launcher baseline (`forge/README.md`).

**`forge/tests/`:**
- Purpose: Own regression and acceptance verification, independently of tutorials.
- Contains: Unit suites, acceptance runner/registry, fixture modules and debugger helper.
- Key files: `forge/tests/README.md`, `forge/tests/test_runner.py`, `forge/tests/acceptance_cases.py`, `forge/tests/test_container_entry.py`.
- Fixture families include `forge/tests/fixtures/basic/`, `forge/tests/fixtures/conan/`, `forge/tests/fixtures/runtime/`, `forge/tests/fixtures/fail/`, `forge/tests/fixtures/consumer/` and `forge/tests/fixtures/versions/`.

**`forge/examples/`:**
- Purpose: Exercise public interfaces from a separate consumer module.
- Contains: Numbered tutorial packages, common checks and example-only dependency recipes.
- Key files: `forge/examples/README.md`, `forge/examples/MODULE.bazel`, `forge/examples/.bazelrc`, `forge/examples/check.py`, `forge/examples/_support/BUILD.bazel`.
- Lesson 09 demonstrates cross-build/static inspection; it is not target deployment evidence. Lesson 10 is roadmap material, not a present example directory (`forge/documents/examples-plan.md`, `forge/README.md`).

**`forge/documents/` versus `documents/`:**
- Purpose: Forge architecture and API documentation lives in `forge/documents/`; the inspected top-level documentation repository is a placeholder.
- Key files: `forge/documents/README.md`, `forge/documents/technical-design.md`, `forge/documents/requirements.md`, `forge/documents/test-design.md`, `documents/README.md`.
- Distinguish design goals in `forge/documents/technical-design.md` from implemented rules and acceptance evidence.

## Key File Locations

**Entry Points:**
- `forge/forge-bazel`: Host-side Bazel command forwarding.
- `forge/forge-shell`: Interactive shell in the same persistent environment.
- `forge/tests/test_runner.py`: Registered acceptance cases and result/evidence handling.
- `forge/bazel/toolchain/export_runtime.sh`: Explicit cross-toolchain runtime archive export.

**Configuration:**
- `forge/MODULE.bazel`: Dependency and toolchain composition.
- `forge/bazel/bazelrc`: Shared Debug/Release, sanitizer and platform presets.
- `forge/examples/.bazelrc`: Consumer import of shared configuration.
- `forge/bazel/toolchain/environment_spec.json`: Environment policy data.
- `forge/docker/image.lock.json`: Reviewed local image/environment identity.
- `forge/bazel/toolchain/generate_files.py`: Generated toolchain/profile source of truth.

**Core Logic:**
- `forge/forge_container.py`: Container contract and instance lifecycle.
- `forge/bazel/cmake_conan_forge.bzl`: Declared exports, action specifications and provider adaptation.
- `forge/bazel/tools/cmake_action.py`: Build/install boundary and final manifests.
- `forge/bazel/tools/conan_action.py`: Isolated acquisition and original-package verification.
- `forge/bazel/runtime.bzl`: Runtime selection/mapping and native publication.
- `forge/bazel/packaging/driver.py`: Validated install layouts and packaging.

**Testing:**
- `forge/tests/test_action_unit.py`: Action-level regressions.
- `forge/tests/test_conan_identity_unit.py`: Dependency identity regressions.
- `forge/tests/test_packaging_unit.py`: Packaging regressions.
- `forge/tests/test_runner_unit.py`: Acceptance-runner behavior.
- `forge/bazel/toolchain/environment_test.py`: Tool environment checks.
- `forge/bazel/packaging/driver_test.py`: Specialized packaging-driver checks.

## Naming Conventions

**Files:**
- Use `BUILD.bazel` and `MODULE.bazel` for Bazel package/module boundaries (`forge/bazel/tools/BUILD.bazel`, `forge/examples/MODULE.bazel`).
- Use snake_case Python and Starlark module names by responsibility (`forge/bazel/tools/action_common.py`, `forge/bazel/config/validation.bzl`).
- Use `test_<area>_unit.py` for central Python unit suites; preserve existing specialized `*_test.py` suites beside their subsystem (`forge/tests/test_runtime_export_unit.py`, `forge/bazel/toolchain/environment_test.py`).
- Keep CMake projects named `CMakeLists.txt`, recipes `conanfile.py`/`conanfile.txt`, and package locks `conan.lock` (`forge/tests/fixtures/conan/`).
- Generated Conan profile names encode preset and mode/sanitizer; build-context profiles include `build` (`forge/bazel/toolchain/conan/linux_x86_64_build_debug.profile`).

**Directories:**
- Use responsibility-oriented directories for production code: `forge/bazel/config/`, `forge/bazel/toolchain/`, `forge/bazel/packaging/`.
- Use numbered kebab-case lessons for examples and short feature names for acceptance fixtures (`forge/examples/07-release-symbols/`, `forge/tests/fixtures/debug/`).
- Keep local recipe indexes under `recipes/<package>/` with version recipe directories and `config.yml` (`forge/examples/_support/conan-index/recipes/tiny_math/`).

## Where to Add New Code

**New build feature:**
- Primary analysis/API code: Extend the responsible file under `forge/bazel/`; shared semantic providers belong in `forge/bazel/providers.bzl`.
- Execution implementation: Extend `forge/bazel/tools/` for build/acquisition behavior or `forge/bazel/packaging/driver.py` for runtime publication.
- Tests: Add focused unit cases under `forge/tests/` and representative `BUILD.bazel` fixture graphs under `forge/tests/fixtures/`.
- Documentation: Update the matching API guide in `forge/documents/`, not the placeholder `documents/README.md`.

**New component/module:**
- Public Starlark APIs: Put the interface beside `forge/bazel/cmake_forge.bzl` or `forge/bazel/runtime.bzl`, and export required assets through the corresponding `BUILD.bazel`.
- New CMake/Conan consumer: Keep `BUILD.bazel`, `CMakeLists.txt`, source, recipe and lock in the consumer package; follow `forge/examples/04-conan-locked/`.
- New example: Add a numbered lesson beneath `forge/examples/`, not production drivers beneath an example. Add regression coverage independently in `forge/tests/` (`forge/AGENTS.md`).

**Platform/toolchain changes:**
- Put normalization in `forge/bazel/config/validation.bzl` and selections in `forge/bazel/config/variants.bzl`.
- Update generator/policy sources in `forge/bazel/toolchain/`; regenerate matching CMake and Conan outputs together.
- Check consistency from `forge/` with `python3 bazel/toolchain/generate_files.py --check` (`forge/AGENTS.md`).
- Image/launcher support changes also require explicit changes and validation at `forge/forge_container.py`, `forge/docker/verify_environment.py` and the reviewed image contract; a profile alone is insufficient.

**Utilities:**
- Shared action helpers: `forge/bazel/tools/action_common.py`.
- Shared example assets: `forge/examples/_support/`; do not make production drivers depend on tutorial assets.
- Shared acceptance helpers and registration: `forge/tests/test_runner.py`, `forge/tests/acceptance_cases.py`.

**Consumer wiring pattern:**

```python
# A sibling consumer's MODULE.bazel follows forge/examples/MODULE.bazel.
bazel_dep(name = "eros_forge", version = "0.1.0")
local_path_override(module_name = "eros_forge", path = "../forge")
```

Adjust the relative override to the actual consumer location; `forge/examples/MODULE.bazel` is nested inside Forge rather than beside it. Import the shared rc from the consumer `.bazelrc` as documented in `forge/README.md`.

## Special Directories

**`.planning/` / `agents/planning/`:**
- Purpose: Codebase maps; `.planning` resolves to `agents/planning/`.
- Generated: Analysis documents are generated, not runtime build outputs.
- Committed: Root `.planning` is ignored by `.gitignore`; underlying documents belong to the separate `agents/` repository. Do not edit another mapper's documents.
- Instruction boundary: Root `AGENTS.md` points to missing `agents/AGENTS.md`; applicable readable product instructions are `forge/AGENTS.md`. No `.kilo/skills/` directory or configured mapper skills are detected.

**`forge/examples/bazel-bin/`, `forge/examples/bazel-out/`, `forge/examples/bazel-testlogs/`, `forge/examples/bazel-examples/`:**
- Purpose: Bazel-generated output convenience links/trees visible in the checkout.
- Generated: Yes.
- Committed: Ignored by `forge/.gitignore` via `bazel-*`; never edit as source (`forge/AGENTS.md`).

**`forge/examples/.forge-evidence/` and `__pycache__/` beneath Forge:**
- Purpose: Local check evidence and Python bytecode cache.
- Generated: Yes.
- Committed: Ignored by `forge/.gitignore`; do not confuse existing evidence directories with verified acceptance results or delete them incidentally.

**`forge/bazel/toolchain/cmake/` and `forge/bazel/toolchain/conan/`:**
- Purpose: Generated build-system projections of the common toolchain policy.
- Generated: Yes.
- Committed: Their preset `.cmake` and `.profile` files are tracked in the Forge repository; change through `forge/bazel/toolchain/generate_files.py`.

**`.repo/`, `.kilo/`, `.vscode/`:**
- Purpose: Workspace management and local editor/agent state.
- Generated: Tool-managed/local, not application source.
- Committed: Ignored by root `.gitignore`; do not use `.kilo/agent-manager.json` as a programmatic API.

---

*Structure analysis: 2026-11-02*
