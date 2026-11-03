# Phase 1: System Composition and Portability Boundary - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-11-03
**Phase:** 1-System Composition and Portability Boundary
**Areas discussed:** Document ownership, System composition model, Portability contract

---

## Document Ownership

### Authoritative location

| Option | Description | Selected |
|--------|-------------|----------|
| documents/ repository | Project-level authoritative documents in `documents/`; planning files only link and summarize. | |
| agents/planning | Scope baseline is a planning artifact in `agents/planning`; `documents/` is not used for planning. | ✓ |
| Both full copies | Keep full content in both locations and synchronize manually. | |

**User's choice:** Scope baseline is a planning artifact in `agents/planning`.
`documents/` is for outward-facing documentation such as architecture
introductions and core-technology explanations. After robot-OS functionality is
modified, corresponding documents under `documents/` must also be updated.

**Notes:** This separates planning authority from external documentation and
avoids maintaining two planning copies.

### Structure

| Option | Description | Selected |
|--------|-------------|----------|
| Single SCOPE.md | One authoritative baseline file; later phases extend sections. | ✓ |
| scope/ multi-file | Separate files for composition, portability, implemented scope, and exclusions. | |
| Expand PROJECT.md only | No new file; add detail to PROJECT.md. | |

**User's choice:** Single `SCOPE.md`.

### Depth

| Option | Description | Selected |
|--------|-------------|----------|
| Responsibilities and boundaries | Purpose, one responsibility, boundaries, and adjacent relationships; no APIs or implementation. | ✓ |
| Add interface contracts | Include inputs, outputs, ownership, and interface summaries. | |
| Add capability matrix | Include implemented/planned/excluded status; overlaps Phase 2. | |

**User's choice:** Responsibilities and boundaries only.

### documents/ synchronization

| Option | Description | Selected |
|--------|-------------|----------|
| Record synchronization rule | Record the future update rule in SCOPE.md; do not create external documents now. | ✓ |
| Create document skeleton | Create architecture and core-technology skeletons now. | |
| Defer | Do not record a rule in this phase. | |

**User's choice:** Record the synchronization rule; do not modify `documents/` in
this milestone.

---

## System Composition Model

### Composition taxonomy

| Option | Description | Selected |
|--------|-------------|----------|
| Five parallel areas | BSP, firmware, robot OS, Forge, CI/CD as peers. | |
| Three groups | Hardware enablement, robot software platform, delivery operations. | |
| Six capability domains | More granular capability-based split. | |
| Two-layer 3+2 | Robot OS product (Linux BSP, firmware, runtime) plus supporting infrastructure (Forge, CI/CD). | ✓ |

**User's choice:** Two-layer 3+2. Forge is supporting infrastructure, not part of
the robot OS runtime. This preserves the distinction between deployed product and
build/delivery support.

### Zephyr relationship

| Option | Description | Selected |
|--------|-------------|----------|
| Firmware foundation | Zephyr is the runtime foundation for embedded board firmware, including motor-control firmware. | ✓ |
| Independent real-time OS area | Zephyr is a separate top-level real-time OS area. | |
| Same-SoC AMP domain | Linux and Zephyr share a SoC in an AMP configuration. | |

**User's choice:** Zephyr is the foundation of embedded firmware. Linux and Zephyr
run on separate boards and communicate over fieldbuses such as CAN; this is not
same-SoC AMP.

### Runtime scope

| Option | Description | Selected |
|--------|-------------|----------|
| Platform services only | Lifecycle, IPC, device abstraction, config, logging, time sync, update/deployment interfaces. | |
| Include application framework | Platform services plus application framework, component model, lifecycle, and package model. | ✓ |
| Linux base only | Init, IPC, driver access, and base libraries only. | |

**User's choice:** Include the application framework in the runtime and
system-services area.

### Forge and CI/CD seam

| Option | Description | Selected |
|--------|-------------|----------|
| Build/orchestration split | Forge owns reproducible builds, dependencies, packaging, and artifacts; CI/CD owns orchestration, tests, release, deployment, and rollback. | ✓ |
| Forge owns release | Forge also owns release; CI/CD only triggers and deploys. | |
| Per-pipeline convention | No fixed seam; each pipeline defines its own split. | |

**User's choice:** Build/orchestration split.

### Bootloader ownership

| Option | Description | Selected |
|--------|-------------|----------|
| Linux BSP | BSP owns bootloader, kernel, drivers, device tree, and rootfs hardware enablement. | ✓ |
| Board firmware | Firmware owns bootloader; BSP owns kernel and drivers only. | |
| Per-platform decision | Leave bootloader ownership to each platform. | |

**User's choice:** Linux BSP owns the boot chain.

### Linux–Zephyr communication

| Option | Description | Selected |
|--------|-------------|----------|
| Three-layer split | BSP provides bus drivers; runtime defines the Linux-side device model and protocol; firmware implements the Zephyr endpoint. | ✓ |
| BSP provides all | BSP owns transport, device model, and protocol. | |
| Firmware provides all | Firmware owns the link including the Linux-side driver. | |

**User's choice:** Linux and external Zephyr boards communicate over buses such
as CAN. The runtime defines the device model and protocol; BSP provides the bus
drivers; firmware implements the real-time endpoint.

### System update

| Option | Description | Selected |
|--------|-------------|----------|
| Layered update | CI/CD release orchestration, runtime update agent, BSP boot slots, firmware update/recovery. | |
| CI/CD end-to-end | CI/CD owns the entire update flow including device-side rollback. | |
| Defer to v2 | Only note that update crosses runtime, BSP, and firmware. | ✓ |

**User's choice:** Defer update/OTA responsibility to v2.

---

## Portability Contract

### Unified source/API layers

| Option | Description | Selected |
|--------|-------------|----------|
| Upper layers unified | Runtime, system services, and application framework keep common source/APIs; BSP and firmware may be platform-specific. | ✓ |
| Product layers mostly unified | Most product layers share source; BSP/firmware use conditional builds. | |
| Application layer only | Only applications are portable; runtime services may diverge. | |

**User's choice:** Upper layers unified.

### Binary compatibility

| Option | Description | Selected |
|--------|-------------|----------|
| No binary guarantee | Rebuild per platform; no cross-platform binary guarantee or extra cross-version ABI promise. | ✓ |
| Same-architecture ABI compatibility | Guarantee ABI compatibility within an architecture family. | |
| Source-only compatibility | Promise source/API compatibility and require rebuild even on the same platform. | |

**User's choice:** Do not promise binary compatibility.

### Platform naming

| Option | Description | Selected |
|--------|-------------|----------|
| Examples only | Use Linux/amd64 development host and ARM64 candidate target as examples, not commitments. | ✓ |
| Commit to two architectures | Name Linux x86_64 and ARM64 as initial target architectures. | |
| Do not name architectures | State only that supported platforms are to be determined. | |

**User's choice:** Use examples without support commitments.

---

## the agent's Discretion

- Section ordering, heading names, and cross-reference formatting in `SCOPE.md`.
- English wording, matching existing planning and codebase documents.
- Visual representation of the two composition layers.

## Deferred Ideas

- System update and OTA responsibility design (v2).
- Application framework implementation.
- External `documents/` architecture and core-technology material until
  functionality exists.
- Forge expansion, BSP implementation, firmware implementation, and CI/CD
  implementation.
