# Phase 1: System Composition and Portability Boundary - Context

**Gathered:** 2026-11-03
**Status:** Ready for planning

<domain>
## Phase Boundary

Define the long-term EROS system composition, the single responsibility of each
composition area, the boundaries between adjacent areas, and the portability
contract. This phase produces planning documentation only. It does not define
APIs or interface fields, implement runtime or firmware, select target hardware,
or claim system acceptance.

</domain>

<decisions>
## Implementation Decisions

### Scope Document Ownership

- **D-01:** The authoritative scope baseline is a single
  `agents/planning/SCOPE.md`. `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md`
  link to it rather than duplicating its full content. — **Reversibility:** costly
  — moving the baseline later would require updating project links, traceability
  references, and phase evidence.
- **D-02:** `documents/` is the outward-facing project documentation repository
  for architecture introductions, core-technology explanations, and similar
  material. It is not the planning baseline. After robot-OS functionality is
  implemented or changed, the corresponding `documents/` material must be
  updated in the same delivery cycle.
- **D-03:** This milestone does not create or modify `documents/` content. The
  synchronization rule above is recorded in `SCOPE.md` for later phases.

### System Composition

- **D-04:** EROS composition uses two layers with five areas total:
  - **Robot OS product layer:** Linux BSP; embedded board firmware; robot
    runtime and system services.
  - **Supporting infrastructure layer:** Forge; CI/CD.
  — **Reversibility:** costly — changing the taxonomy later would invalidate the
  responsibility map and downstream phase mappings.
- **D-05:** **Linux BSP** owns the Linux boot chain: bootloader, kernel, drivers,
  device tree, and rootfs hardware enablement for the main compute board. It does
  not own embedded-board application firmware. — **Reversibility:** costly —
  moving boot-chain ownership would change hardware bring-up and update
  responsibilities.
- **D-06:** **Embedded board firmware** is Zephyr-based firmware for external
  boards, including motor-control and other real-time embedded boards. Zephyr is
  the foundation of this area, not a separate top-level area. Firmware implements
  the Zephyr-side endpoints and real-time behavior.
- **D-07:** **Robot runtime and system services** owns process and service
  lifecycle, IPC, device-access abstraction, configuration, logging, time
  synchronization, and the application framework, component model, lifecycle,
  and package model. It is the Linux-side software platform, not the Linux BSP.
  — **Reversibility:** costly — this scope defines the public software platform
  boundary used by later runtime and application phases.
- **D-08:** **Forge** owns reproducible builds, dependency management, packaging,
  and artifact production. **CI/CD** owns pipeline orchestration, test execution,
  release, deployment, and rollback. Forge does not own pipeline orchestration,
  and CI/CD consumes Forge outputs rather than redefining build semantics.
- **D-09:** Linux and external Zephyr boards communicate over fieldbuses such as
  CAN; the architecture is not same-SoC AMP. The runtime defines the Linux-side
  device model and communication protocol; the BSP provides the fieldbus drivers;
  firmware implements the Zephyr-side endpoint under its real-time constraints.
  — **Reversibility:** costly — the protocol and device-model owner determines
  cross-area compatibility and testing responsibilities.
- **D-10:** System update and OTA responsibility is deferred to v2. `SCOPE.md`
  states only that updates cross the runtime, BSP, and firmware areas; it does not
  assign update orchestration, boot-slot, or firmware-recovery ownership.

### Portability Contract

- **D-11:** The robot runtime and system services, including the application
  framework, keep common source and public APIs across supported platforms where
  practical. Linux BSP and embedded firmware may have platform-specific
  implementations behind those boundaries.
- **D-12:** EROS does not guarantee identical binaries across platforms. Each
  platform is rebuilt for its target; no additional cross-version ABI
  compatibility is promised in this baseline. — **Reversibility:** costly — this
  is a published compatibility boundary that later consumers may rely on.
- **D-13:** Linux/amd64 is named only as the current development-host example and
  ARM64 only as a candidate target example. Neither is a support commitment or a
  hardware selection.

### Documentation Depth

- **D-14:** `SCOPE.md` documents purpose, one responsibility per area, boundaries
  between adjacent areas, and the portability contract. It does not define APIs,
  message fields, interface signatures, or implementation designs. — **Reversibility:** reversible.

### the agent's Discretion

- `SCOPE.md` section ordering, heading names, and cross-reference formatting.
- English wording, matching the existing English planning and codebase documents.
- How to represent the two layers visually, provided the five areas and their
  layer membership remain explicit.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Project and Planning

- `.planning/PROJECT.md` — EROS identity, core value, active requirements, and
  constraints.
- `.planning/REQUIREMENTS.md` — `SCOPE-01` and `SCOPE-04` define this phase.
- `.planning/ROADMAP.md` — Phase 1 goal, dependencies, and success criteria.
- `agents/AGENTS.md` — multi-repository rules, planning ownership, and evidence
  boundaries. The root `AGENTS.md` is a symbolic link to this file.

### Codebase Evidence

- `.planning/codebase/ARCHITECTURE.md` — implemented Forge architecture and the
  boundary between build infrastructure and robot-system scope.
- `.planning/codebase/STRUCTURE.md` — repository layout, Forge module roots, and
  the role of `agents/`, `documents/`, and `forge/`.
- `.planning/codebase/CONVENTIONS.md` — documentation and evidence conventions;
  no formatter or lint policy is established.
- `.planning/codebase/CONCERNS.md` — known acceptance and evidence gaps; do not
  present Forge implementation as system acceptance.

### Forge Boundaries

- `forge/AGENTS.md` — Forge scope, controlled-environment rules, and verification
  boundaries.
- `forge/README.md` — current supported environment and consumer interface.
- `forge/documents/requirements.md` — Forge requirements and scope.
- `forge/documents/technical-design.md` — Forge architecture and mechanism
  contracts.
- `forge/documents/test-design.md` — Forge acceptance criteria and evidence
  boundaries.

### Workspace and External Documentation

- `.repo/manifests/eros.xml` — repository membership and branch selection.
- `documents/README.md` — placeholder for the outward-facing documentation
  repository; no external architecture or core-technology documents exist yet.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets

- `agents/AGENTS.md`: provides the EROS identity, multi-repository boundary, and
  evidence rules that `SCOPE.md` must remain consistent with.
- `forge/AGENTS.md`: provides the authoritative Forge responsibility and
  verification boundaries.
- `.planning/codebase/`: provides evidence for which capabilities are implemented
  today and which are not.

### Established Patterns

- Planning documents use Markdown with explicit headings and file-path references.
- Evidence is separated into requirements, design, implementation, test results,
  and target acceptance; the baseline must preserve that distinction.
- Project-level planning is owned by `agents/planning/`; `documents/` is not a
  planning artifact repository.

### Integration Points

- `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` reference the new
  `agents/planning/SCOPE.md`.
- `documents/` will later receive outward-facing architecture and core-technology
  material after robot-OS functionality exists.
- Phase 2 adds implemented-scope and exclusion detail to `SCOPE.md`; Phase 3
  reviews cross-document consistency and traceability.

</code_context>

<specifics>
## Specific Ideas

- The composition is two layers: **robot OS product** (Linux BSP, embedded board
  firmware, robot runtime and system services) and **supporting infrastructure**
  (Forge, CI/CD).
- Linux runs on the main compute board; Zephyr runs on external embedded boards
  such as motor controllers. They communicate over fieldbuses such as CAN, not
  through same-SoC AMP.
- The runtime owns the fieldbus device model and protocol; the BSP supplies bus
  drivers; firmware owns the real-time Zephyr endpoint.
- The application framework belongs inside the runtime and system-services area.
- `documents/` is for external readers and must be synchronized after functional
  changes, not used as the planning source of truth.

</specifics>

<deferred>
## Deferred Ideas

- **System update / OTA design** — deferred to v2; only the cross-area dependency
  is noted in this phase.
- **Application framework implementation** — the responsibility belongs to the
  runtime area, but implementation is future scope.
- **External `documents/` architecture and core-technology material** — deferred
  until robot-OS functionality exists and can be documented accurately.
- **Forge expansion, BSP implementation, firmware implementation, and CI/CD
  implementation** — outside this milestone's scope.

</deferred>

---

*Phase: 1-System Composition and Portability Boundary*
*Context gathered: 2026-11-03*
