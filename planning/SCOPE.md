# EROS Scope Baseline

**Defined:** 2026-11-03
**Last updated:** 2026-11-03

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

## Documentation Boundary

- `documents/` is the outward-facing project documentation repository for architecture introductions, core-technology explanations, and similar material; it is not the planning baseline.
- After robot-OS functionality is implemented or changed, update the corresponding `documents/` material in the same delivery cycle.
- This baseline does not create or modify `documents/` content; this rule is recorded for later phases.

## Deferred Responsibilities

- System update and OTA are deferred to v2. This baseline states only that updates cross the runtime, BSP, and firmware areas and does not assign update orchestration, boot-slot, or firmware-recovery ownership.
- Application-framework implementation remains future scope, and the responsibility belongs to the runtime and system-services area.

---

*Scope baseline defined: 2026-11-03*
*Last updated: 2026-11-03*
