---
phase: 02-implemented-scope-and-explicit-exclusions
plan: 01
subsystem: planning-documentation
tags: [scope-baseline, forge-capabilities, exclusions, evidence-boundaries, planning]

requires:
  - phase: 01-system-composition-and-portability-boundary
    provides: "Authoritative two-layer scope baseline with composition areas, adjacent boundaries, portability contract, and link-not-copy references"
provides:
  - "Eight-group evidence-backed inventory of Forge's implemented capabilities with FR/NFR refs and implementation boundaries"
  - "Nine-row explicit EROS exclusion table with status, reason, and exit criterion"
  - "Milestone-level note that exclusions are not permanent non-goals"
affects: [03-baseline-traceability-and-consistency-review]

actuals:
  tokens: 2200
  tasks: 3
  commits: 2

tech-stack:
  added: []
  patterns:
    - "Capability inventory rows pair implementation evidence with an explicit boundary column"
    - "Scope status vocabulary separate from acceptance-runner result states"
    - "Exclusion rows carry an actionable exit criterion"

key-files:
  created: []
  modified:
    - agents/planning/SCOPE.md

key-decisions:
  - "Extend agents/planning/SCOPE.md in place between Portability Contract and Documentation Boundary; do not create a second scope document (D-01)."
  - "Use exactly eight Forge capability groups in the D-04 order and keep partial scope in the boundary column (D-04, D-06)."
  - "Cite repository-relative code paths plus authoritative Forge documents as implementation-presence evidence only (D-07, D-13, D-14)."
  - "Use exactly nine exclusion rows in the D-09 order with only Not implemented, Not selected, Not verified, and Deferred statuses (D-09, D-10)."
  - "Record the milestone-level note so exclusions are not read as permanent non-goals (D-12)."

patterns-established:
  - "Evidence cells combine code paths and authoritative documents; no test or acceptance pass is claimed."
  - "Scope exclusions use a closed four-value status vocabulary distinct from PASS/FAIL/SKIP/BLOCKED."
  - "Every exclusion row is paired with a verifiable exit criterion."

requirements-completed: [SCOPE-02, SCOPE-03]

coverage:
  - id: D1
    description: "Eight-row Forge capability inventory with D-04 groups, FR/NFR references, code-plus-document evidence, and D-08 implementation boundaries."
    requirement: SCOPE-02
    verification:
      - kind: other
        ref: "Task 1 and Task 3 bash validation suite (heading order, row count/order, sub-capability counts, identifier allow-list, evidence path existence and tracking, D-08 facts, guard sentence)"
        status: pass
    human_judgment: true
    rationale: "Normative capability and boundary wording requires human review beyond literal string checks."
  - id: D2
    description: "Nine-row explicit exclusion table with exact areas, statuses, reasons, exit criteria, and milestone-level note."
    requirement: SCOPE-03
    verification:
      - kind: other
        ref: "Task 2 and Task 3 bash validation suite (heading order, row count/order, area/status sequences, reason and exit-criterion length, framing paragraph, milestone note)"
        status: pass
    human_judgment: true
    rationale: "Exclusion reasons and exit criteria require human judgment that the wording matches project intent."
  - id: D3
    description: "SCOPE.md preserves Phase 1 content; documents/ and forge/ remain unchanged; no duplicated tables in other planning documents."
    verification:
      - kind: other
        ref: "Normalized snapshot diff, representative Phase 1 literal checks, repository status checks, table-header count checks, git diff --check, trailing-whitespace check"
        status: pass
    human_judgment: false

duration: 2min
completed: 2026-11-04
status: complete
plan_head_before: 64c8f711473496fa4b46b8754c6cf09c567c853c
plan_head_after: ce64fe8dda734895531dbbdf01c896ed2f43d8cb
---

# Phase 2 Plan 01: Implemented Scope and Explicit Exclusions Summary

**Eight-group Forge capability inventory and nine-row EROS exclusion table added to the authoritative SCOPE.md baseline, with code-plus-document evidence and explicit implementation boundaries.**

## Performance

- **Duration:** 2min
- **Started:** 2026-11-03T18:22:57Z
- **Completed:** 2026-11-03T18:24:49Z
- **Tasks:** 3
- **Files modified:** 1

## Accomplishments

- Inserted `## Implemented Forge Capabilities` between `## Portability Contract` and `## Documentation Boundary` with exactly eight capability groups in the D-04 order.
- Each Forge row cites published FR/NFR identifiers, at least two repository-relative `forge/` code paths, and at least one authoritative `forge/documents/` reference; every cited path exists and is tracked in the Forge repository.
- Preserved every D-08 limitation in the boundary column: Linux/amd64-only local entry and build flow, incomplete acceptance-registry coverage (`TC-ENV-05`-`TC-ENV-07`, `TC-ENTRY-01`-`TC-ENTRY-05`, `TC-CI-03`), unestablished ARM64 native execution and registry multi-architecture images, no matching target SDK or target execution, no remote-cache evidence, incomplete CI acceptance, and the `--all --strict` limitation.
- Inserted `## Explicit Exclusions` with exactly nine rows in the D-09 order, using only the four allowed scope statuses, with a specific reason and actionable exit criterion per row.
- Added the required exclusion framing paragraph, milestone-level note, and evidence-vs-acceptance guard sentence, each on one physical line.
- Updated both `Last updated` lines to 2026-11-04 while leaving the defined-date lines at 2026-11-03.
- Confirmed the Phase 1 sections are unchanged via a normalized full-text diff against the pre-edit snapshot.
- Confirmed `documents/` and `forge/` remained clean and no `SKELETON.md` or `COVERAGE.md` was created.

## Task Commits

Each content-changing task was committed atomically in the `agents` repository:

1. **Task 1: Insert the eight-row Implemented Forge Capabilities section into SCOPE.md** - `f06e79b` (docs)
2. **Task 2: Insert the nine-row Explicit Exclusions section into SCOPE.md** - `ce64fe8` (docs)
3. **Task 3: Validate SCOPE.md structure, evidence grounding, and repository boundaries** - validation-only; no content changes and therefore no separate commit (covered by the plan metadata commit)

**Plan metadata:** `docs(02-01): complete scope inventory plan` in the `agents` repository.

_Measured commits from the plan ledger: `64c8f71..ce64fe8` = 2._

## Files Created/Modified

- `agents/planning/SCOPE.md` - Added the eight-row Implemented Forge Capabilities table with FR/NFR references, code-plus-document evidence, and D-08 boundaries; added the nine-row Explicit Exclusions table with statuses, reasons, exit criteria, and the milestone-level note; updated both Last updated lines to 2026-11-04. Phase 1 sections unchanged.

## Decisions Made

- Extended the existing authoritative `SCOPE.md` in place; no second scope document was created (D-01).
- Kept the Forge inventory at exactly eight capability groups in the D-04 order, with partial or unaccepted scope in each row's boundary column rather than the exclusion table (D-04, D-06).
- Cited only published FR-01 through FR-20 and NFR-01 through NFR-04 identifiers; no identifier or mapping was invented (D-05).
- Described evidence as implementation presence only; no test, suite, deployment, or target acceptance pass is claimed (D-07, D-13, D-14).
- Used exactly nine exclusion rows in the D-09 order and only `Not implemented`, `Not selected`, `Not verified`, and `Deferred` as statuses (D-09, D-10).
- Added the milestone-level note so the exclusions are not read as permanent non-goals and do not redefine the Phase 1 composition (D-12).
- Left the Forge/CI-CD boundary and the two-layer composition intact; Forge is not described as the whole operating system (D-15).

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None. The Task 1 pre-edit snapshot `/tmp/eros-scope-before.md` was available, so the normalized full-text preservation diff ran and showed no change outside the two inserted sections and the two `Last updated` lines.

## Validation

All commands were run from the workspace root `/home/user/programs/eros-ws/eros`; all exited 0.

| Check | Result |
|-------|--------|
| `agents/planning/SCOPE.md` exists | PASS |
| Exact nine level-2 headings in order, with both new sections between Portability Contract and Documentation Boundary | PASS |
| Exact Forge and exclusion table header rows | PASS |
| Forge table has exactly eight five-cell rows in D-04 order | PASS |
| Each Forge sub-capabilities cell has 2-4 non-empty comma-separated items | PASS |
| Each Forge row cites FR/NFR and combines at least two `forge/` paths with one `forge/documents/` document | PASS |
| Only FR-01..FR-20 and NFR-01..NFR-04 identifiers appear | PASS |
| Every cited evidence path exists and is tracked in the `forge` repository | PASS |
| All thirteen D-08 boundary facts appear in the boundary column | PASS |
| Exclusion table has exactly nine five-cell rows in D-09 order | PASS |
| Exclusion area and status sequences match the mandated values | PASS |
| Every Reason and Exit criterion cell is non-empty and substantive | PASS |
| Exclusion framing paragraph, milestone-level note, and evidence guard sentence present verbatim | PASS |
| Both `Last updated` lines read 2026-11-04 and both defined-date lines remain 2026-11-03 | PASS |
| Representative Phase 1 normative literals unchanged | PASS |
| Normalized snapshot diff shows no change outside the inserted sections and Last updated lines | PASS |
| Each new table header appears exactly once across SCOPE.md, PROJECT.md, REQUIREMENTS.md, and ROADMAP.md | PASS |
| `git -C documents status --porcelain` empty after a successful git invocation | PASS |
| `git -C forge status --porcelain` empty after a successful git invocation | PASS |
| `git -C agents diff --check` clean | PASS |
| No trailing whitespace in the checked planning files | PASS |
| `SKELETON.md` and `COVERAGE.md` absent from the phase directory | PASS |

No Forge acceptance, Docker, Bazel, or target test was run. Task 3 performed 27 labeled validation checks in total; all passed.

## Known Stubs

None. This is a documentation-only plan; no component, data source, or UI stub was introduced.

## Threat Flags

None. No new network endpoint, auth path, file-access pattern, or schema at a trust boundary was introduced beyond the plan's threat model.

## Next Phase Readiness

- `SCOPE.md` now answers SCOPE-02 with the eight-group Forge inventory and SCOPE-03 with the nine-row explicit exclusion table, ready for Phase 3 traceability and cross-document consistency review.
- `documents/` and `forge/` remain untouched.
- `REQUIREMENTS.md` was not modified, per the execution constraints; SCOPE-02 and SCOPE-03 completion should be recorded by the phase tracking/transition step.
- No blockers or concerns.

## Self-Check: PASSED

- `agents/planning/SCOPE.md` exists.
- `agents/planning/SCOPE.md` contains `## Implemented Forge Capabilities` and `## Explicit Exclusions`.
- Commit `f06e79b` exists in `agents` and is an ancestor of HEAD.
- Commit `ce64fe8` exists in `agents` and is an ancestor of HEAD.
- Plan ledger measurement `64c8f71..ce64fe8` equals 2 commits.
- `documents/` and `forge/` are clean.

---
*Phase: 02-implemented-scope-and-explicit-exclusions*
*Completed: 2026-11-04*
