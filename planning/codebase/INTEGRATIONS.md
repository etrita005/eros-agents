# External Integrations

**Analysis Date:** 2026-11-02

## APIs & External Services

**Local container execution:**
- Docker daemon: persistent controlled build containers, image inspection, bind mounts, and command execution (`forge/forge_container.py`).
  - SDK/Client: Python standard-library `subprocess` invoking the Docker CLI; no Python Docker SDK.
  - Auth: host user's Docker daemon access. No application API-key environment variable is defined.
  - Contract: exact image ID/platform from `forge/docker/image.lock.json`, caller UID/GID, host networking, and verified container mounts/environment. Launchers do not pull or publish images.

**Source hosting and workspace acquisition:**
- GitHub HTTPS hosts the `eros-agents`, `eros-documents`, and `eros-forge` component repositories declared in `.repo/manifests/eros.xml`.
  - SDK/Client: Git and `repo`; the workspace manifest includes `eros.xml` through `.repo/manifest.xml`.
  - Auth: no source-host credential integration is declared in those manifest files. Do not infer authenticated publishing from repository URLs.

**Builder image and tool acquisition:**
- Ubuntu base image and Noble APT repositories supply operating-system/compiler packages (`forge/Dockerfile.amd64`, `forge/docker/ubuntu.sources`).
  - SDK/Client: Docker builder and APT.
  - Endpoint: Tsinghua Ubuntu mirror; APT configuration references the Ubuntu archive keyring.
  - Auth: no service credentials declared in these files.
- Python packages supply CMake 4.4.4, Conan 2.25.2, and their transitive dependencies (`forge/docker/requirements.in`, `forge/docker/requirements.lock`).
  - SDK/Client: pip with `--require-hashes`; the Dockerfile does not declare a custom Python index.
  - Auth: no package-index credential mechanism is configured by `forge/Dockerfile.amd64`.
- Bazel 9.0.0 is downloaded from `releases.bazel.build`, with a GitHub release fallback and SHA256 verification (`forge/Dockerfile.amd64`).
  - SDK/Client: curl during image construction.
  - Ordinary launchers require the prepared locked image; image acquisition/build is a separate explicit maintenance operation (`forge/README.md`).
- Bazel Bzlmod resolves rule/platform dependencies using the module graph and lockfile (`forge/MODULE.bazel`, `forge/MODULE.bazel.lock`). Cold builds need module-source network access unless inputs are provisioned (`forge/README.md`).

**Conan package repositories:**
- Configurable HTTP(S) Conan remotes supply declared recipes and exact pinned binaries; no mandatory hosted production Conan endpoint is hardcoded in `forge/bazel/tools/conan_action.py`.
  - SDK/Client: controlled Conan CLI, invoked using the environment manifest's Python and Conan paths (`forge/bazel/tools/action_common.py`).
  - Configuration: a declared `remote_config` JSON document, schema version 2, containing `remotes`, `packages`, and complete build-variant identity (`forge/bazel/tools/conan_action.py`).
  - Auth: no production credential provider or token pass-through is implemented by the inspected action code. Do not assume host Conan credentials/remotes are available; the action uses its own private HOME/CONAN_HOME (`forge/bazel/tools/action_common.py`, `forge/bazel/tools/conan_action.py`).
  - Integrity: pinned binaries are fetched and their complete files/symlinks verified before use; a failed pin is fatal, not a source-fallback opportunity. Missing unpinned variants may build from locked sources (`forge/bazel/tools/conan_action.py`).
  - Alternatives: declared `file_remote` recipe indices, `local_recipes`, and `cache_archive` inputs; these are explicit action inputs rather than implicit host caches (`forge/bazel/tools/conan_action.py`, `forge/README.md`).
  - Publication: ordinary consumer builds do not upload packages; remote service operations and producer publishing belong to maintainers (`forge/README.md`).

**Example dependency downloads:**
- GitHub release archives supply Protobuf/protoc 36.2 and Abseil 20260107.1 through checked Conan recipe metadata (`forge/examples/_support/conan-index/recipes/protobuf/all/conandata.yml`, `forge/examples/_support/conan-index/recipes/protoc/all/conandata.yml`, `forge/examples/_support/conan-index/recipes/abseil/all/conandata.yml`).
  - SDK/Client: Conan recipe helpers, with pinned archive digests.
  - Protobuf is consumed as recipe version 7.36.2; protoc is a build-context tool, not a target runtime executable (`forge/examples/04b-cmake-protobuf/conanfile.py`).
  - No network Protobuf or gRPC service is implied: the example generates C++ locally in the CMake binary directory (`forge/examples/04b-cmake-protobuf/CMakeLists.txt`).

**Acceptance-only external services:**
- A disposable loopback Conan HTTP server is available through explicit `conan-server==2.33.0` bootstrap; it is not a production service or ordinary dependency-build requirement (`forge/tests/README.md`).
- Remote cache/independent-machine and target-runtime acceptance require external preparation/evidence. Do not treat those acceptance designs as deployed integrations (`forge/tests/README.md`, `forge/README.md`).

## Data Storage

**Databases:**
- Not detected in the build implementation; state consists of filesystem trees and JSON manifests, not a database/ORM (`forge/forge_container.py`, `forge/bazel/tools/cmake_action.py`).

**File Storage:**
- Local source trees and required read-only Git metadata are mounted into the persistent container. Instance state is bound at its original absolute path under `~/.cache/eros-forge/<key>` (`forge/forge_container.py`).
- Bazel output bases, generated environment manifests, installs, debug/source inventories, tar/deb artifacts, and acceptance evidence remain local unless an operator publishes them (`forge/bazel/tools/cmake_action.py`, `forge/tests/README.md`).
- No S3/object-storage client or configured hosted artifact bucket is detected in these execution paths (`forge/forge_container.py`, `forge/bazel/tools/action_common.py`, `forge/README.md`).

**Caching:**
- Bazel result/output state persists beneath instance HOME; launchers reject an effective output base outside the current instance state (`forge/forge_container.py`).
- Conan caches are private action-scoped homes, not the user's shared host cache; cleanup is checked before final manifest publication (`forge/bazel/tools/conan_action.py`, `forge/bazel/tools/cmake_action.py`).
- Matching declared remote Conan binaries avoid dependency compilation; a Bazel final-result cache hit avoids the whole combined action. Custom actions prohibit remote execution, not caching (`forge/README.md`).
- No shared remote-cache service endpoint is established by `forge/bazel/bazelrc`; independent-host cache verification is a separate evidence scope (`forge/tests/README.md`).

## Authentication & Identity

**Auth Provider:**
- No end-user authentication provider, OAuth flow, or application session system is detected; this is build infrastructure (`forge/README.md`).
- Local authority comes from Docker access and caller UID/GID. Container execution drops capabilities and requires no-new-privileges, but Docker daemon access remains a privileged host concern (`forge/forge_container.py`, `forge/README.md`).
- Artifact/environment identity is hash-based: image ID, tool-environment digest, SDK identity, dependency locks, binary file hashes, and symlink targets. This is provenance/integrity validation, not user authentication (`forge/docker/image.lock.json`, `forge/bazel/tools/conan_action.py`).

## Monitoring & Observability

**Error Tracking:**
- No hosted error-tracking SDK is detected in the execution paths. Failures surface through subprocess exit status, Python exceptions, and CLI diagnostics (`forge/bazel/tools/action_common.py`, `forge/bazel/tools/cmake_action.py`).

**Logs:**
- Build commands print a `Forge:` prefix; subprocess output is streamed or captured according to the caller (`forge/bazel/tools/action_common.py`). Keep credentials out of command arguments because this helper prints argv.
- Acceptance evidence includes per-case results, command logs, argv/cwd, selected environment, duration, exit code, source/lock digests, and environment provenance (`forge/tests/README.md`, `forge/tests/test_runner.py`).
- Reports distinguish PASS, FAIL, SKIP, and BLOCKED. Non-strict exit zero is not complete acceptance (`forge/tests/README.md`).
- No centralized log collector or telemetry service is declared by the inspected builder configuration (`forge/Dockerfile.amd64`, `forge/bazel/bazelrc`).

## CI/CD & Deployment

**Hosting:**
- Local Docker builder only; the approved image is a local artifact, not a published registry artifact (`forge/README.md`, `forge/docker/image.lock.json`).
- Delivery outputs target private Linux runtime layouts and Debian package installation under `/opt/eros/<package>` (`forge/bazel/deb.bzl`, `forge/README.md`).
- Cross-runtime export discovers AArch64 GCC runtime libraries and emits a checked archive, but does not deploy it or establish complete SDK/target ABI readiness (`forge/bazel/toolchain/export_runtime.sh`, `forge/README.md`).

**CI Pipeline:**
- No checked-in GitHub Actions pipeline is detected in the workspace scan. The reusable acceptance runner and test-package interface are CI building blocks, not evidence of an operational hosted release pipeline (`forge/tests/test_runner.py`, `forge/README.md`).
- Publishing Conan binaries, hosting remotes, and validating independent-machine cache reuse remain explicit maintainer/external scopes (`forge/README.md`, `forge/tests/README.md`).

## Environment Configuration

**Required env vars:**
- No application API token, database URL, or SaaS credential variable is required by the inspected build drivers (`forge/forge_container.py`, `forge/bazel/tools/action_common.py`).
- `HOME` must support writable state outside source workspaces; the launcher derives isolated container HOME and TMPDIR (`forge/forge_container.py`).
- Optional `FORGE_INSTANCE` selects the instance name; only letters, digits, underscore, and hyphen are accepted (`forge/forge_container.py`).
- Optional `HTTP_PROXY`, `HTTPS_PROXY`, `NO_PROXY` and lowercase equivalents are forwarded to container commands; forwarding to Conan actions requires explicit Bazel action environment configuration (`forge/forge_container.py`, `forge/bazel/tools/conan_action.py`, `forge/README.md`).
- `DEB_VERSION` is forwarded by the launcher and declared in the shared Bazel action environment (`forge/forge_container.py`, `forge/bazel/bazelrc`).
- `EROS_FORGE_IMAGE_ID` and `EROS_FORGE_ENVIRONMENT_SHA256` are launcher-produced provenance claims, not secrets or user-configured credentials (`forge/forge_container.py`).
- `CONAN_HOME`, `EROS_ENVIRONMENT_ID`, and `EROS_CAPTURE_SOURCES` are action-generated; preserve their isolation rather than setting them to host cache locations (`forge/bazel/tools/conan_action.py`).
- Test-only controls include `FORGE_TEST_DOCKER` and `FORGE_GDB`; see `forge/tests/README.md`.

Use an already configured proxy without embedding its value in source:

```bash
./forge-bazel --bazelrc=bazel/bazelrc build \
  --action_env=HTTPS_PROXY //tests/fixtures/basic:native
```

Run from `forge/`; the example demonstrates the explicit action-environment mechanism documented in `forge/README.md`, not a requirement of the native fixture itself.

**Secrets location:**
- No project secret store is identified from the inspected non-secret configuration. Secret files and credential-bearing configuration are outside this audit; no values are read or copied.
- Keep proxy/package-service credentials in operator-controlled runtime configuration, not source, image configuration, generated manifests, or committed evidence (`forge/AGENTS.md`, `forge/README.md`).
- Host Conan state is intentionally not mounted/used by normal consumer actions; adding authenticated remotes requires an explicit safe integration rather than assuming credential inheritance (`forge/bazel/tools/conan_action.py`).

## Webhooks & Callbacks

**Incoming:**
- No application webhook endpoint is detected. The optional loopback Conan server serves test repository traffic, not application callbacks (`forge/tests/README.md`).

**Outgoing:**
- No notification/payment/deployment webhook is detected. Outbound integrations are dependency downloads and Docker CLI control; consumer Conan actions do not publish (`forge/Dockerfile.amd64`, `forge/bazel/tools/conan_action.py`, `forge/forge_container.py`).
- Build-time CMake/protoc invocation is an executable integration, not a network callback (`forge/examples/04b-cmake-protobuf/CMakeLists.txt`).

---

*Integration audit: 2026-11-02*
