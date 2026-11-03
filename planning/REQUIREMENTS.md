# Requirements: EROS

**Defined:** 2026-11-03
**Core Value:** Provide a unified, verifiable software foundation for robot hardware across supported platforms while keeping build and delivery reproducible.

## v1 Requirements

Requirements for the initial scope baseline. Each maps to a roadmap phase.
System composition and portability detail is defined in `.planning/SCOPE.md`.

### Scope

- [x] **SCOPE-01**: Define the long-term EROS system composition and the responsibility of each composition area.
- [ ] **SCOPE-02**: Define Forge's implemented capabilities and boundaries separately from unimplemented EROS system scope.
- [ ] **SCOPE-03**: Record explicit exclusions for this baseline so future runtime, hardware, firmware, and CI/CD work is not presented as delivered.
- [x] **SCOPE-04**: Define the portability boundary: common source and APIs where practical, platform-specific builds where required, and no guarantee of identical binaries across platforms.

### Baseline Review

- [ ] **TRACE-01**: Map every v1 requirement to exactly one roadmap phase and record its evidence status.
- [ ] **REVIEW-01**: Review `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` for consistency and record unresolved items.

## v2 Requirements

Deferred to future milestones. Tracked but not in the current roadmap.

### Runtime

- **RUNTIME-01**: Define and implement a minimal robot runtime, including process lifecycle, communication, and deployment.
- **RUNTIME-02**: Define the application framework and package model for robot components.

### Hardware Support

- **BSP-01**: Deliver a BSP for the first selected robot controller platform.
- **DRIVER-01**: Provide device driver integration for selected sensors and actuators.
- **FIRMWARE-01**: Define the board firmware build, update, and recovery path.

### Delivery

- **CI-01**: Implement CI pipelines for build, test, packaging, and artifact publication.
- **DEPLOY-01**: Implement deployment and rollback automation for target robots.
- **ACCEPT-01**: Establish target-hardware system acceptance with execution evidence.

## Out of Scope

Explicitly excluded from this scope baseline. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Robot runtime or middleware implementation | This milestone defines scope only; no runtime exists in the workspace |
| BSP, device driver, or firmware implementation | No target hardware or board support is selected or validated |
| Forge build, packaging, or acceptance expansion | Forge has its own subproject scope and planning boundary |
| CI/CD or deployment automation implementation | Only responsibility boundaries are documented in this milestone |
| Target ABI, deployment, or robot-system acceptance claims | No target execution evidence exists in the workspace |
| Hardware selection or procurement | This baseline does not choose a production platform |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| SCOPE-01 | Phase 1 | Complete |
| SCOPE-02 | Phase 2 | Pending |
| SCOPE-03 | Phase 2 | Pending |
| SCOPE-04 | Phase 1 | Complete |
| TRACE-01 | Phase 3 | Pending |
| REVIEW-01 | Phase 3 | Pending |

**Coverage:**
- v1 requirements: 6 total
- Mapped to phases: 6
- Unmapped: 0

---
*Requirements defined: 2026-11-03*
*Last updated: 2026-11-03 after roadmap creation*
