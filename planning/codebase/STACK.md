# Technology Stack

**Analysis Date:** 2026-11-02

## Languages

**Primary:**
- Python 3: host Docker transport, execution-time build drivers, environment validation, packaging, and acceptance runners. Host launchers require Python >=3.9; build-side policy checks Python major 3. See `forge/forge_container.py`, `forge/bazel/tools/cmake_action.py`, `forge/bazel/toolchain/environment_spec.json`, and `forge/tests/test_runner.py`.
- Starlark (Bazel 9): build rules, providers, platform configuration, and toolchain registration. Put analysis-time interfaces in `forge/bazel/*.bzl`; put execution-time drivers in `forge/bazel/tools/` or `forge/bazel/packaging/`, as required by `forge/AGENTS.md`.

**Secondary:**
- C++20/GNU C++20: native/CMake consumer examples and test fixtures. Generated Conan profiles select `compiler.cppstd=gnu20`; the Protobuf consumer declares `cxx_std_20`. See `forge/bazel/toolchain/generate_files.py` and `forge/examples/04b-cmake-protobuf/CMakeLists.txt`.
- Bash 4+: cross-runtime export, backed by GNU coreutils, tar/gzip, and cross-binutils; see `forge/bazel/toolchain/export_runtime.sh` and `forge/README.md`.
- CMake language: package-local configuration, installation, and code generation; see `forge/examples/04b-cmake-protobuf/CMakeLists.txt` and `forge/bazel/toolchain/cmake/`.
- JSON, YAML, XML, and Markdown: environment/pin manifests, recipe metadata, multi-repository manifests, and documentation; see `forge/docker/image.lock.json`, `forge/examples/_support/conan-index/`, and `.repo/manifests/eros.xml`.

## Runtime

**Environment:**
- Controlled Linux Docker builder, not a web application runtime. The supported local entry is native Linux/amd64 with a local Docker daemon; see `forge/README.md` and `forge/forge_container.py`.
- Ubuntu Noble package sources and a digest-pinned Ubuntu base image; see `forge/docker/ubuntu.sources` and `forge/Dockerfile.amd64`. Noble is Ubuntu 24.04; Python lock generation targets Python 3.12, but do not confuse that target with an exact runtime patch-version guarantee (`forge/docker/requirements.lock`).
- GCC/G++ major 13 and matching native/AArch64 binutils provide native and cross compilation. Exact tool contents are captured and validated separately from version policy in `forge/bazel/toolchain/environment.py` and `forge/bazel/toolchain/environment_spec.json`.
- Container commands run as the caller's UID/GID with host networking, dropped capabilities, and no-new-privileges; see `forge/forge_container.py`.

**Package Manager:**
- Bazel Bzlmod 9.0.0 resolves the build-rule graph; lockfiles are present at `forge/MODULE.bazel.lock` and `forge/examples/MODULE.bazel.lock`.
- Conan 2.25.2 supplies declared C/C++ dependencies; dependency locks and checked recipe indices are consumer inputs. See `forge/docker/requirements.in`, `forge/bazel/tools/conan_action.py`, and `forge/examples/_support/conan-index/`.
- pip installs hashed Python/tool dependencies from `forge/docker/requirements.lock`; `uv pip compile` generates that lock, but uv is not a required ordinary-build runtime (`forge/docker/requirements.lock`).
- APT supplies compiler/system tools during image construction. Package names are declared, not individually version-pinned in the Dockerfile; image identity and environment capture constrain the accepted build environment (`forge/Dockerfile.amd64`, `forge/docker/image.lock.json`).
- Git and Google's `repo` organize the three component repositories: `agents`, `documents`, and `forge` (`.repo/manifests/eros.xml`). There is no root application package manifest; `agents/README.md` and `documents/README.md` are minimal repository placeholders.

## Frameworks

**Core:**
- Bazel 9.0.0 owns inter-package orchestration; the module is `eros_forge` 0.1.0 (`forge/MODULE.bazel`, `forge/bazel/toolchain/environment_spec.json`).
- CMake 4.4.4 owns package-internal builds and install layouts (`forge/docker/requirements.in`, `forge/bazel/tools/cmake_action.py`). Example minimum CMake versions can be lower than the controlled builder version; the Protobuf example requires at least 3.24 (`forge/examples/04b-cmake-protobuf/CMakeLists.txt`).
- Conan 2.25.2 handles locked third-party acquisition and generation within the same action lifetime as CMake (`forge/bazel/tools/conan_action.py`, `forge/bazel/tools/cmake_action.py`).
- No application web framework, database ORM, or frontend runtime is detected in the implementation. The executable scope is build/delivery infrastructure (`forge/README.md`, `forge/AGENTS.md`).

**Testing:**
- Python standard-library `unittest` for launcher and build-side unit suites; no separately versioned pytest/Jest/Vitest runner is required by the documented commands (`forge/tests/README.md`).
- Custom Python acceptance runner reports PASS/FAIL/SKIP/BLOCKED and provenance; `--all --strict` is not a complete release gate because not all designed cases are registered (`forge/tests/test_runner.py`, `forge/tests/README.md`).
- Real Bazel/CMake/Conan builds, ELF inspection, GDB, and sanitizer diagnostics support integration checks (`forge/tests/README.md`, `forge/bazel/bazelrc`).
- Optional test-only `conan-server==2.33.0` is explicitly bootstrapped for loopback binary/revision tests; it is not a normal build dependency (`forge/tests/README.md`).

**Build/Dev:**
- Docker CLI/daemon, version not pinned in repository policy; use `forge/forge-bazel` or `forge/forge-shell`, never substitute host build tools (`forge/AGENTS.md`).
- GNU make, m4, patch, binutils, dpkg-dev, GDB, Git, curl, and archive utilities are installed by `forge/Dockerfile.amd64`.
- Sanitizer configurations: ASan, UBSan, TSan, and combined ASan/UBSan. Debug and Release are explicit Bazel rc configurations; Release enables separate debug information (`forge/bazel/bazelrc`).
- clangd configuration template exists at `forge/bazel/clangd-template.yaml`; this does not establish Clang as the compilation toolchain.

## Key Dependencies

**Critical:**
- `rules_cc` 0.2.14: Bazel C/C++ rule/toolchain support (`forge/MODULE.bazel`).
- `rules_pkg` 1.0.1: packaging support (`forge/MODULE.bazel`, `forge/bazel/deb.bzl`).
- `platforms` 1.0.0: target/execution platform constraints (`forge/MODULE.bazel`).
- `rules_foreign_cc` 0.15.1: foreign-build tooling integration; controlled CMake/make/m4 toolchains are registered in `forge/MODULE.bazel` and defined in `forge/bazel/tools/foreign_tools.bzl`.
- Preserve immutable Conan recipe/package identities and complete per-file/symlink binary pins; `forge/bazel/tools/conan_action.py` rejects mismatches and unpinned cached binaries.

**Infrastructure:**
- Python drivers predominantly use the standard library, including `subprocess`, `pathlib`, `hashlib`, `json`, and Linux `fcntl`; see `forge/forge_container.py` and `forge/bazel/tools/action_common.py`.
- Protobuf recipe 7.36.2, build-context protoc 36.2, and Abseil 20260107.1 are real example dependencies, not unconditional Forge runtime dependencies (`forge/examples/04b-cmake-protobuf/conanfile.py`, `forge/examples/_support/conan-index/recipes/protobuf/all/conanfile.py`).
- Protocol generation is local CMake work, not evidence of a deployed Protobuf/gRPC service (`forge/examples/04b-cmake-protobuf/CMakeLists.txt`).

## Configuration

**Environment:**
- `forge/docker/image.lock.json` is the local image/platform/environment/SDK authority. The current SDK identity is explicitly missing at `/opt/eros`; target deployment acceptance is unavailable from that fact alone.
- `forge/bazel/toolchain/environment_spec.json` defines GCC-major/version policy and native/cross presets; `forge/bazel/toolchain/environment.py` captures actual tool identities.
- Forwarded host variables are HTTP(S)/NO_PROXY names in both cases and `DEB_VERSION`; `FORGE_INSTANCE` selects instance identity. Build-action proxy values require explicit Bazel `--action_env` forwarding (`forge/forge_container.py`, `forge/README.md`).
- Each build action receives a private HOME; Conan additionally gets a private CONAN_HOME and noninteractive execution. Keep locale and reproducibility environment centralized in `forge/bazel/tools/action_common.py`.
- No project skill directory is present under `.kilo/`, and the configured mapper-skill query returns no skills. Use the applicable contributor constraints in `forge/AGENTS.md`; the root `AGENTS.md` is a broken symlink in this checkout.

**Build:**
- Module definitions: `forge/MODULE.bazel`, `forge/examples/MODULE.bazel`.
- Shared rc and platform/sanitizer selection: `forge/bazel/bazelrc`, `forge/bazel/config/`.
- Native/runtime/deb interfaces: `forge/bazel/runtime.bzl`, `forge/bazel/deb.bzl`.
- CMake/Conan interfaces: `forge/bazel/cmake_forge.bzl`, `forge/bazel/cmake_conan_forge.bzl`.
- Generate derived toolchain files through `forge/bazel/toolchain/generate_files.py`; do not hand-edit generated profiles or Bazel output trees (`forge/AGENTS.md`).

Use the controlled entry from the Forge repository root:

```bash
./forge-bazel --bazelrc=bazel/bazelrc build //bazel/toolchain:validate_environment
./forge-bazel --bazelrc=bazel/bazelrc run --config=debug //tests/fixtures/basic:native
```

These commands follow `forge/README.md`; they are documented usage, not execution evidence from this mapping.

## Platform Requirements

**Development:**
- Native Linux x86_64, Python >=3.9, accessible local Docker, approved local image, writable source/output locations, and sufficient disk bytes/inodes. Network access is needed for unprovisioned dependencies (`forge/README.md`).
- State lives under the caller's `~/.cache/eros-forge/<key>`; use separate checkouts for concurrent container instances, rather than sharing Bazel output links (`forge/forge_container.py`).
- Do not automatically pull/rebuild an image or change locks to bypass prerequisite failures (`forge/AGENTS.md`).

**Production:**
- Outputs are private runtime tarballs, Debian packages, paired debug packages, and test closures; application payloads use `/opt/eros/<package>/{bin,lib,share}` (`forge/README.md`, `forge/bazel/deb.bzl`).
- ARM64 cross tools are implemented, but a matching SDK, target loader/libraries, and target execution evidence are separate requirements. `/opt/eros` is not automatically a complete compiler sysroot (`forge/bazel/toolchain/environment_spec.json`, `forge/README.md`).
- Native ARM64 presets and `forge/Dockerfile.arm64` exist, but ARM64 hosts, remote/rootless Docker, macOS, and Windows are not validated local entry configurations (`forge/README.md`).
- No deployed cloud/application hosting target or operational publishing pipeline is established by the inspected source; Forge supplies build/export mechanisms (`forge/README.md`, `forge/tests/README.md`).

---

*Stack analysis: 2026-11-02*
