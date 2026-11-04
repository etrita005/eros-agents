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
  composition area; see `.planning/SCOPE.md` — v1.0.
- ✓ Defined the portability boundary: common source and public APIs where
  practical, platform-specific builds where required, and no guarantee of
  identical binaries across platforms; see `.planning/SCOPE.md` — v1.0.
- ✓ Inventoried Forge's implemented capabilities with FR/NFR references,
  code-plus-document evidence, and explicit implementation boundaries; see
  `.planning/SCOPE.md` — v1.0.
- ✓ Recorded explicit EROS-scope exclusions with status, reason, and exit
  criterion; see `.planning/SCOPE.md` — v1.0.
- ✓ Established requirement-to-phase traceability with the full evidence chain
  in the archived v1.0 requirements — v1.0.
- ✓ Reviewed the baseline planning documents, dispositioned every finding in
  `.planning/milestones/v1.0-phases/03-baseline-traceability-and-consistency-review/03-REVIEW.md`,
  and recorded prior-verification impact — v1.0.

### Active

None — all v1 requirements for this milestone are validated.

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
- `forge/` is the only subproject with substantial implementation. `agents/`
  contains the project-level planning baseline in `agents/planning/`, and
  `documents/` remains repository scaffolding for outward-facing project
  documentation.
- The codebase map in `.planning/codebase/` records the implemented Forge
  architecture, conventions, testing, integrations, and concerns.
- The project-level planning artifacts are owned by the `agents` repository
  through the `.planning/` symlink.
- Forge's controlled environment is Linux/amd64 with Docker and a locked local
  image. Cross-compilation or packaging does not establish target deployment.
- The authoritative system composition and portability baseline is `.planning/SCOPE.md`; this document records project-level status rather than duplicating that baseline.

## Current State

- v1.0 Scope Baseline shipped 2026-11-04: 3 phases, 3 plans, and 6/6 v1 requirements verified.
- The planning baseline is authoritative in `.planning/SCOPE.md`; the v1.0 traceability and requirements are archived with the milestone.
- Forge remains the only substantial implementation. No robot runtime, BSP, firmware, CI/CD, deployment, or target acceptance exists.
- Known tech debt: OTA deferral propagation, REVIEW-01 three/four-document wording, and manual-only Nyquist coverage for all three phases.

## Next Milestone Goals

- Define the next milestone scope and requirements before implementation.
- Address the recorded tech debt where it affects the next scope.
- Do not treat runtime, BSP, firmware, OTA, or CI/CD as delivered until planned, implemented, and verified.

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
| Initialize GSD at the EROS workspace root | Cross-repository scope and subproject boundaries need one project-level baseline | ✓ Good (v1.0) |
| Keep Forge as a subproject, not the EROS roadmap | Forge is build and delivery infrastructure, not the whole robot operating system | ✓ Good (v1.0) |
| Limit this baseline to scope and traceability | The workspace has no implemented robot runtime or target hardware evidence | ✓ Good (v1.0) |
| Keep planning artifacts in `agents/planning/` | `.planning/` is owned by the `agents` repository and shared by workspace tooling | ✓ Good (v1.0) |
| Keep the scope baseline in `agents/planning/SCOPE.md` | One authoritative composition baseline avoids divergent copies in `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` | ✓ Good (v1.0) |
| Use a two-layer 3+2 composition model | Separates deployed robot OS product areas from build and delivery infrastructure | ✓ Good (v1.0) |
| Keep partial Forge scope in the capability boundary column | Preserves implementation evidence without misclassifying unaccepted scope as an EROS exclusion | ✓ Good (v1.0) |
| Use a seven-column full traceability chain | Makes requirement, phase, plan, summary, verification, evidence type, and status explicit | ✓ Good (v1.0) |
| Resolve baseline conflicts by domain authority | Prevents a single document from silently overriding another domain's authority | ✓ Good (v1.0) |

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
*Last updated: 2026-11-04 after v1.0 milestone*
