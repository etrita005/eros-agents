# EROS Scope Baseline

**Defined:** 2026-11-03
**Last updated:** 2026-11-04

## Purpose

This document defines the intended long-term EROS system composition, the single
responsibility of each composition area, the boundaries between adjacent areas,
and the platform portability contract. This document defines intended composition
and scope. It is not implementation, test, deployment, or robot-system acceptance
evidence. It does not define APIs, message fields, interface signatures, or implementation designs.

## System Composition

EROS has two composition layers and five areas total. The robot OS product layer
contains the Linux BSP, embedded board firmware, and the robot runtime and system
services. The supporting infrastructure layer contains Forge and CI/CD.

```text
Robot OS product layer
  Linux BSP | Embedded board firmware | Robot runtime and system services

Supporting infrastructure layer
  Forge | CI/CD
```

All five areas are peers within their layer; layer membership is organizational, not a build or dependency order.

## Area Responsibilities

| Area | Layer | Responsibility | Boundary |
|------|-------|----------------|----------|
| Linux BSP | Robot OS product | Owns the Linux boot chain for the main compute board: bootloader, kernel, drivers, device tree, and rootfs hardware enablement. | Does not own embedded-board application firmware. |
| Embedded board firmware | Robot OS product | Provides Zephyr-based firmware for external boards, including motor-control and other real-time embedded boards; implements the Zephyr-side endpoints and real-time behavior. | Zephyr is the foundation of this area, not a separate top-level area; does not own the Linux boot chain. |
| Robot runtime and system services | Robot OS product | Owns process and service lifecycle, IPC, device-access abstraction, configuration, logging, time synchronization, and the application framework, component model, lifecycle, and package model. | Is the Linux-side software platform, not the Linux BSP. |
| Forge | Supporting infrastructure | Owns reproducible builds, dependency management, packaging, and artifact production. | Does not own pipeline orchestration; is not part of the robot OS product. |
| CI/CD | Supporting infrastructure | Owns pipeline orchestration, test execution, release, deployment, and rollback. | Consumes Forge outputs rather than redefining build semantics; is not part of the robot OS product. |

## Adjacent-Area Boundaries

| Adjacent areas | Boundary rule | Explicitly not |
|----------------|---------------|----------------|
| Robot runtime and system services ↔ Linux BSP | The runtime defines the Linux-side device model and communication protocol; the BSP provides the fieldbus drivers. | The BSP does not define runtime device semantics. |
| Linux BSP and runtime ↔ Embedded board firmware | Linux and external Zephyr boards communicate over fieldbuses such as CAN; firmware implements the Zephyr-side endpoint under its real-time constraints. | Not same-SoC AMP. |
| Forge ↔ CI/CD | CI/CD consumes Forge build and package outputs. | Forge does not own pipeline orchestration; CI/CD does not redefine build semantics. |
| System update/OTA ↔ runtime, BSP, and firmware | Updates cross the runtime, BSP, and firmware areas. | No update orchestration, boot-slot, or firmware-recovery ownership is assigned in v1; deferred to v2. |

## Portability Contract

| Contract | Rule | Explicitly not guaranteed |
|----------|------|---------------------------|
| Common source and public APIs | The robot runtime and system services, including the application framework, keep common source and public APIs across supported platforms where practical. | Identical internal implementations across platforms. |
| Platform-specific implementation | The Linux BSP and embedded board firmware may have platform-specific implementations behind those boundaries. | A single shared binary for all platforms. |
| Build model | Identical binaries are not guaranteed across platforms; each platform is rebuilt for its target. No additional cross-version ABI compatibility is promised. | Identical binaries across platforms; additional cross-version ABI compatibility. |
| Named examples | Linux/amd64 is the current development-host example; ARM64 is a candidate target example. | A support commitment or hardware selection. |

## Implemented Forge Capabilities

This table records implementation presence, not test success, deployment, or target acceptance. Evidence cites a repository-relative code path and the authoritative Forge document that defines or describes the mechanism; a design document, recipe, example, or unexecuted check is not a passing result.

| Capability group | Implemented sub-capabilities | Forge requirement refs | Evidence | Implementation boundary / not covered |
|------------------|------------------------------|------------------------|----------|---------------------------------------|
| Controlled build environment and entry | persistent locked container, host launcher passthrough, image and mount validation, environment verification | FR-01, FR-19, FR-20; NFR-02, NFR-04 | `forge/forge_container.py`, `forge/docker/verify_environment.py`; `forge/README.md` §Environment And Setup, `forge/documents/requirements.md` §3.1 | Local entry and build flow are Linux/amd64 only. ARM64 native execution and a validated ARM64 launcher baseline are not established. |
| Bazel orchestration and toolchain | module composition, tool identity capture, variant normalization, generated toolchain files | FR-02, FR-10, FR-19; NFR-01 | `forge/MODULE.bazel`, `forge/bazel/bazelrc`, `forge/bazel/toolchain/host_tools.bzl`; `forge/documents/technical-design.md` §2-§3 | Four presets are defined, but registry multi-architecture images and remote-cache evidence are not established. |
| Native C++ build path | native C++ rules, runtime and debug providers, strict exported outputs | FR-05, FR-15; NFR-01 | `forge/bazel/runtime.bzl`; `forge/documents/technical-design.md` §6 | Build and ELF evidence only; a matching target SDK and target execution are not established, so target ABI and deployment acceptance are not claimed. |
| Foreign CMake integration | foreign_cc overlay, install-tree adaptation, strict export validation | FR-05, FR-10, FR-15, FR-18 | `forge/bazel/cmake_forge.bzl`; `forge/documents/cmake-forge.md` §Install And Export | Build and ELF evidence only; a matching target SDK and target execution are not established, so target ABI and deployment acceptance are not claimed. |
| Locked Conan + CMake integration | single-action Conan and CMake, lockfile enforcement, original-package verification, provenance manifests | FR-03, FR-04, FR-10, FR-18; NFR-02 | `forge/bazel/cmake_conan_forge.bzl`, `forge/bazel/tools/cmake_action.py`, `forge/bazel/tools/conan_action.py`; `forge/documents/cmake-conan-forge.md` §Action And Final Outputs | Build and ELF evidence only; a matching target SDK and target execution are not established, so target ABI and deployment acceptance are not claimed. |
| Runtime closure | runfiles selection, transitive closure, plugin and resource roots, ownership mappings | FR-06, FR-07, FR-13, FR-15 | `forge/bazel/runtime.bzl`, `forge/bazel/deb.bzl`, `forge/bazel/packaging/driver.py`; `forge/documents/technical-design.md` §6 | Closure computation is implemented, but target install, target execution, and robot-system acceptance are not verified. |
| Debian and symbol packaging | Debian payload assembly, split-configuration multiarch, paired dbgsym, external manifests | FR-09, FR-11, FR-16, FR-17; NFR-01 | `forge/bazel/deb.bzl`, `forge/bazel/packaging/driver.py`, `forge/bazel/toolchain/export_runtime.sh`; `forge/documents/deb-packaging.md` §Debug And Test Archives | Debian payloads are produced locally; target install/uninstall, target execution, and symbol use on a target are not verified. |
| Acceptance and verification tooling | registered acceptance cases, separate result-state reporting, unit and driver tests, environment checks | FR-14, FR-17, FR-19; NFR-03 | `forge/tests/test_runner.py`, `forge/tests/acceptance_cases.py`, `forge/bazel/toolchain/environment_test.py`, `forge/bazel/packaging/driver_test.py`; `forge/documents/test-design.md` §1, `forge/tests/README.md` §Results And Evidence | Acceptance registry coverage is incomplete: `TC-ENV-05`-`TC-ENV-07`, `TC-ENTRY-01`-`TC-ENTRY-05`, and `TC-CI-03` are not fully registered. Complete CI acceptance and target execution are not established. `--all --strict` is not by itself proof of complete release acceptance. |

## Explicit Exclusions

The following EROS system capabilities are excluded from this milestone baseline. Status describes scope, not an acceptance-runner result.

| Excluded capability | Area | Status | Reason | Exit criterion |
|---------------------|------|--------|--------|----------------|
| Robot runtime and system services implementation | Robot runtime and system services | Not implemented | No runtime source exists in this workspace; the area is defined only as intended composition. | An approved runtime implementation exists with implementation evidence and its own acceptance scope. |
| Application framework, component model, lifecycle, and package model | Robot runtime and system services | Not implemented | The application framework is assigned to the runtime area in the composition, but no framework, component model, lifecycle, or package-model implementation exists in this workspace. | An approved application-framework implementation exists with a defined component and package model and its own acceptance scope. |
| Linux BSP and hardware support | Linux BSP | Not selected | No target compute board or supported Linux boot chain is selected, and the workspace contains no BSP source or hardware enablement. | A target board is selected with an approved BSP, boot chain, and hardware-enablement evidence. |
| Zephyr-based embedded board firmware | Embedded board firmware | Not selected | No external embedded board or Zephyr firmware project is selected, and the workspace contains no firmware source. | A target embedded board is selected with implemented Zephyr firmware and board-level evidence. |
| CI/CD pipeline and orchestration implementation | CI/CD | Not implemented | The workspace contains no pipeline configuration, orchestration service, or automated release infrastructure; the area is defined only as intended composition. | An approved CI/CD pipeline exists that consumes Forge outputs and records its own execution evidence. |
| Deployment and rollback automation | CI/CD | Not implemented | No deployment, rollback, or target-release automation exists in this workspace. | Approved deployment and rollback automation exists with target execution evidence. |
| Target hardware selection | Cross-cutting | Not selected | No production or development target platform has been chosen; Linux/amd64 and ARM64 remain examples, not selections. | A target platform is approved and recorded with its platform, ABI, and support boundary. |
| Target ABI, deployment, and robot-system acceptance | Cross-cutting | Not verified | Local builds, cross-compilation, and ELF inspection do not establish target ABI agreement, deployment, or robot-system acceptance, and no target execution evidence exists. | Target execution evidence exists for a matching target SDK and runtime, covering ABI, deployment, and robot-system acceptance. |
| System update / OTA design and implementation | Cross-cutting | Deferred | Update orchestration, boot-slot ownership, and firmware recovery are deferred to v2 and are not assigned in this baseline. | An approved update/OTA design exists with assigned ownership across runtime, BSP, and firmware, plus implementation and acceptance evidence. |

These are milestone-level exclusions, not permanent non-goals. They do not redefine the long-term composition in the sections above.

## Documentation Boundary

- `documents/` is the outward-facing project documentation repository for architecture introductions, core-technology explanations, and similar material; it is not the planning baseline.
- After robot-OS functionality is implemented or changed, update the corresponding `documents/` material in the same delivery cycle.
- This baseline does not create or modify `documents/` content; this rule is recorded for later phases.

## Deferred Responsibilities

- System update and OTA are deferred to v2. This baseline states only that updates cross the runtime, BSP, and firmware areas and does not assign update orchestration, boot-slot, or firmware-recovery ownership.
- Application-framework implementation remains future scope, and the responsibility belongs to the runtime and system-services area.

---

*Scope baseline defined: 2026-11-03*
*Last updated: 2026-11-04*
