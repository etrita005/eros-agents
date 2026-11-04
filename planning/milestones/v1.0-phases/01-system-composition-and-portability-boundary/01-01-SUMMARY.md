---
phase: 01-system-composition-and-portability-boundary
plan: 01
subsystem: planning-documentation
tags: [scope-baseline, system-composition, portability, planning, traceability]

requires: []
provides:
  - "Authoritative EROS scope baseline in agents/planning/SCOPE.md"
  - "Two-layer, five-area system composition with one responsibility and adjacent boundary per area"
  - "Portability contract with common source/public APIs, platform-specific implementations, no identical-binary guarantee, and no additional cross-version ABI promise"
  - "docs boundary rule for documents/ synchronization in the same delivery cycle"
  - "Authoritative-baseline links in PROJECT.md, REQUIREMENTS.md, and ROADMAP.md"
affects: [02-implemented-scope-and-explicit-exclusions, 03-baseline-traceability-and-consistency-review]

actuals:
  tokens: 2000
  tasks: 3
  commits: 3

tech-stack:
  added: []
  patterns:
    - "Single authoritative scope baseline with link-not-copy references from project planning documents"
    - "Area / Layer / Responsibility / Boundary table shape for composition areas"
    - "Contract / Rule / Explicitly not guaranteed table shape for the portability boundary"

key-files:
  created:
    - agents/planning/SCOPE.md
  modified:
    - agents/planning/PROJECT.md
    - agents/planning/REQUIREMENTS.md
    - agents/planning/ROADMAP.md

key-decisions:
  - "Keep the scope baseline in agents/planning/SCOPE.md and link it from PROJECT.md, REQUIREMENTS.md, and ROADMAP.md rather than duplicating tables (D-01)."
  - "Record the documents/ same-delivery-cycle synchronization rule without creating or modifying documents/ content (D-02, D-03)."
  - "Keep the D-01 Key Decisions outcome as Pending; documentation existence is not decision-completion evidence."
  - "Keep the Task 2 link rows as single contiguous lines so the authoritative-baseline literals are verifiable."

patterns-established:
  - "Link-not-copy: one baseline owns composition and portability content; other planning documents cite it."
  - "Normative scope wording stays contiguously greppable for automated validation."

requirements-completed: [SCOPE-01, SCOPE-04]

coverage:
  - id: D1
    description: "Authoritative EROS scope baseline with two layers, five areas, per-area responsibilities, adjacent boundaries, portability contract, documentation boundary, and deferred responsibilities."
    requirement: SCOPE-01
    verification:
      - kind: other
        ref: "bash validation suite from the workspace root (title, headings, five-row table structure, D-02 through D-14 literals)"
        status: pass
    human_judgment: true
    rationale: "Normative scope wording and boundary fidelity require human review beyond literal string checks."
  - id: D2
    description: "PROJECT.md, REQUIREMENTS.md, and ROADMAP.md link to .planning/SCOPE.md as the authoritative baseline without duplicating composition or portability tables."
    requirement: SCOPE-04
    verification:
      - kind: other
        ref: "grep checks for the PROJECT.md link bullet and D-01 row, REQUIREMENTS.md introduction sentence, ROADMAP.md Phase 1 label, and absence of duplicated table headers"
        status: pass
    human_judgment: false
  - id: D3
    description: "documents/ and forge/ repositories remain unchanged, and the agents repository has no whitespace errors."
    verification:
      - kind: other
        ref: "git -C documents status --porcelain; git -C forge status --porcelain; git -C agents diff --check; trailing-whitespace grep"
        status: pass
    human_judgment: false

duration: 1min
completed: 2026-11-04
status: complete
plan_head_before: 417dea5b841ad351632077614ec9cbe54abb9f69
plan_head_after: a84e90e30a06c09bdcbc753a2f091cd26fcf42d5
---

# Phase 1 Plan 01: Authoritative Scope Baseline Summary

**Two-layer, five-area EROS scope baseline with per-area responsibilities, explicit adjacent boundaries, a portability contract, and link-not-copy references from PROJECT.md, REQUIREMENTS.md, and ROADMAP.md.**

## Performance

- **Duration:** 1min
- **Started:** 2026-11-03T16:20:01Z
- **Completed:** 2026-11-03T16:21:08Z
- **Tasks:** 3
- **Files modified:** 4

## Accomplishments

- Created `agents/planning/SCOPE.md` as the single authoritative scope baseline: two composition layers, five areas, one responsibility and adjacent boundary per area, portability contract, documentation boundary, and deferred responsibilities.
- Recorded the Linux BSP boot-chain ownership, Zephyr-based embedded firmware endpoints, runtime-owned Linux-side device model and protocol, and fieldbus (such as CAN) communication instead of same-SoC AMP.
- Recorded the portability contract: common source and public APIs for the runtime and application framework, platform-specific BSP and firmware implementations, no guarantee of identical binaries across platforms, and no additional cross-version ABI compatibility.
- Linked `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` to `.planning/SCOPE.md` without duplicating the composition, boundary, or portability tables, and recorded the D-01 decision row as `— Pending`.
- Confirmed `documents/` and `forge/` remained unchanged and all 14 automated validation checks passed.

## Task Commits

Each task was committed atomically in the `agents` repository:

1. **Task 1: Create the authoritative EROS scope baseline in SCOPE.md** - `8c66234` (docs)
2. **Task 2: Link PROJECT.md, REQUIREMENTS.md, and ROADMAP.md to SCOPE.md** - `acdd95d` (docs)
3. **Task 3: Validate the scope baseline, cross-document links, and documentation boundaries** - `a84e90e` (docs)

**Plan metadata:** committed as `docs(01-01): complete scope baseline plan` in the `agents` repository.

_Measured commits from the plan ledger: `417dea5..a84e90e` = 3._

## Files Created/Modified

- `agents/planning/SCOPE.md` - Authoritative scope baseline: purpose, system composition, area responsibilities, adjacent-area boundaries, portability contract, documentation boundary, and deferred responsibilities.
- `agents/planning/PROJECT.md` - Added the authoritative-baseline Context bullet and the D-01 Key Decisions row; preserved the subproject-capability evidence comment.
- `agents/planning/REQUIREMENTS.md` - Added the SCOPE.md introduction sentence under v1 Requirements; requirement text, traceability rows, and Pending statuses unchanged.
- `agents/planning/ROADMAP.md` - Added the Phase 1 `**Scope baseline:**` label; goals, success criteria, plan placeholder, and progress rows unchanged.

## Decisions Made

- Kept `SCOPE.md` as the single source of composition, boundary, and portability content; the other planning documents only link to it (D-01).
- Kept the D-01 Key Decisions outcome as `— Pending` because document existence is not decision-completion evidence.
- Recorded the `documents/` same-delivery-cycle synchronization rule in `SCOPE.md` only, without creating or modifying `documents/` content (D-02, D-03).
- Used responsibility-and-boundary wording only; no APIs, message fields, interface signatures, implementation designs, or hardware support commitments were added (D-13, D-14).

## Validation

All commands below were run from the workspace root `/home/user/programs/eros-ws/eros`; all exited 0.

| Check | Result |
|-------|--------|
| Four planning files exist | PASS |
| Exact title `# EROS Scope Baseline` | PASS |
| Seven required `##` headings present | PASS |
| Five area names and two layer names present | PASS |
| `## Area Responsibilities` has exactly five data rows with non-empty Responsibility and Boundary cells | PASS |
| D-02 through D-14 normative literals present | PASS |
| PROJECT.md baseline link and D-01 decision row present | PASS |
| REQUIREMENTS.md baseline link present | PASS |
| ROADMAP.md Phase 1 Scope baseline label present | PASS |
| No duplicated composition or portability table headers in linked documents | PASS |
| `git -C documents status --porcelain` empty (git invocation succeeded) | PASS |
| `git -C forge status --porcelain` empty (git invocation succeeded) | PASS |
| `git -C agents diff --check` clean | PASS |
| No trailing whitespace in the four changed files | PASS |

## Deviations from Plan

None - plan executed exactly as written. The Task 3 content fix noted below was within Task 3's explicitly authorized scope.

## Issues Encountered

- The first Task 3 run failed the contiguous-literal check for `does not define APIs, message fields, interface signatures, or implementation designs` because the sentence was line-wrapped. Task 3 explicitly authorizes fixing content created by Tasks 1-2, so the sentence was joined onto one line in `SCOPE.md`, re-validated, and committed as `a84e90e`. No scope changed.

## Known Stubs

None. This is a documentation-only plan; no component, data source, or UI stub was introduced.

## Threat Flags

None. No new network endpoint, auth path, file-access pattern, or schema at a trust boundary was introduced beyond the plan's threat model.

## Next Phase Readiness

- The authoritative scope baseline is in place and linked from all three planning documents, ready for Phase 2 (`SCOPE-02`/`SCOPE-03`) to add implemented scope and explicit exclusions.
- `documents/` remains untouched and its synchronization rule is recorded for later phases.
- No blockers or concerns.

## Self-Check: PASSED

- `agents/planning/SCOPE.md` exists.
- `agents/planning/PROJECT.md` exists.
- `agents/planning/REQUIREMENTS.md` exists.
- `agents/planning/ROADMAP.md` exists.
- Commit `8c66234` exists in `agents`.
- Commit `acdd95d` exists in `agents`.
- Commit `a84e90e` exists in `agents`.

---
*Phase: 01-system-composition-and-portability-boundary*
*Completed: 2026-11-04*
