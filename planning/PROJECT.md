# EROS

## What This Is

EROS is a robot operating system project managed as multiple independent Git
repositories with `repo`. The workspace currently contains build and delivery
infrastructure in `forge/`; robot runtime, hardware support, firmware, and
system-level integration are project scope, not implemented capabilities.

## Core Value

Provide a unified, verifiable software foundation for robot hardware across
supported platforms while keeping build and delivery reproducible.

## Requirements

### Validated

<!-- Existing capabilities established by the codebase map. These are
subproject capabilities, not EROS system-level acceptance. -->

- ✓ Forge runs builds inside a locked, controlled Linux/amd64 container rather
  than substituting host build tools — existing.
- ✓ Forge composes builds through Bazel, package-local CMake, and locked Conan
  dependencies — existing.
- ✓ Forge produces runtime artifacts, Debian packages, debug symbol packages,
  and provenance manifests — existing.
- ✓ Forge provides unit and acceptance tooling with distinct PASS, FAIL, SKIP,
  and BLOCKED results — existing.
- ✓ EROS repositories are organized by a `repo` manifest with per-repository
  branches — existing.
- ✓ Defined the long-term EROS system composition and the responsibility of each
  composition area; see `.planning/SCOPE.md` — Phase 1.
- ✓ Defined the portability boundary: common source and public APIs where
  practical, platform-specific builds where required, and no guarantee of
  identical binaries across platforms; see `.planning/SCOPE.md` — Phase 1.
- ✓ Inventoried Forge's implemented capabilities with FR/NFR references,
  code-plus-document evidence, and explicit implementation boundaries; see
  `.planning/SCOPE.md` — Phase 2.
- ✓ Recorded explicit EROS-scope exclusions with status, reason, and exit
  criterion; see `.planning/SCOPE.md` — Phase 2.

### Active

- [ ] **TRACE-01**: Establish requirement-to-phase traceability with evidence
  status for the baseline.
- [ ] **REVIEW-01**: Review `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` for
  consistency and record unresolved items.

### Out of Scope

<!-- Milestone-level exclusions. These are project scope, not permanent
anti-features. -->

- Implementing a robot runtime, middleware, or application framework — this
  milestone establishes scope only.
- Implementing BSPs, board firmware, device drivers, or hardware support — no
  target platform is selected or validated here.
- Expanding Forge build, packaging, or acceptance capabilities — Forge work
  requires its own subproject planning.
- Implementing CI/CD pipelines or deployment automation — only their
  responsibility boundaries are documented.
- Claiming target ABI compatibility, hardware deployment, or robot-system
  acceptance — no target execution evidence exists in this workspace.

## Context

- The workspace root is an EROS multi-repository workspace, not an application
  repository.
- `forge/` is the only subproject with substantial implementation. `agents/` and
  `documents/` currently contain repository scaffolding.
- The codebase map in `.planning/codebase/` records the implemented Forge
  architecture, conventions, testing, integrations, and concerns.
- The project-level planning artifacts are owned by the `agents` repository
  through the `.planning/` symlink.
- Forge's controlled environment is Linux/amd64 with Docker and a locked local
  image. Cross-compilation or packaging does not establish target deployment.
- The authoritative system composition and portability baseline is `.planning/SCOPE.md`; this document records project-level status rather than duplicating that baseline.

## Constraints

- **Repository structure**: EROS uses `repo` to manage independent repositories;
  files must be committed in their owning repository.
- **Planning ownership**: Project-level planning belongs to `agents/planning/`;
  Forge implementation and detailed Forge verification belong to `forge/`.
- **Build environment**: Forge builds must use `forge/forge-bazel` or
  `forge/forge-shell`; host Bazel or host toolchain substitution is not accepted.
- **Evidence boundary**: Requirements, design documents, implementation, test
  results, and target acceptance are distinct states.
- **Milestone scope**: This baseline produces scope, requirements, and roadmap
  documents only; it does not deliver robot functionality.

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Initialize GSD at the EROS workspace root | Cross-repository scope and subproject boundaries need one project-level baseline | — Pending |
| Keep Forge as a subproject, not the EROS roadmap | Forge is build and delivery infrastructure, not the whole robot operating system | — Pending |
| Limit this baseline to scope and traceability | The workspace has no implemented robot runtime or target hardware evidence | — Pending |
| Keep planning artifacts in `agents/planning/` | `.planning/` is owned by the `agents` repository and shared by workspace tooling | — Pending |
| Keep the scope baseline in `agents/planning/SCOPE.md` | One authoritative composition baseline avoids divergent copies in `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-11-04 after Phase 2*
