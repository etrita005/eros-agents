# Phase 3: Baseline Traceability and Consistency Review - Context

**Gathered:** 2026-11-04
**Status:** Ready for planning

<domain>
## Phase Boundary

Establish full v1 requirement traceability and review the baseline planning
documents for consistency. This phase produces planning and review artifacts
only. It does not implement runtime, BSP, firmware, Forge, CI/CD, deployment, or
hardware support; it does not run Forge acceptance or target tests; and it does
not claim target ABI, deployment, or robot-system acceptance.

</domain>

<decisions>
## Implementation Decisions

### Traceability Chain

- **D-01:** Extend the existing `## Traceability` section in
  `agents/planning/REQUIREMENTS.md`; do not create a separate traceability file.
  The table must use exactly these seven columns:
  `| Requirement | Phase | Plan | Summary | Verification | Evidence type | Status |`
- **D-02:** Use the full chain for every v1 requirement: requirement → phase →
  delivering plan → plan summary → phase verification report → evidence type and
  status. Use repository-relative `.planning/phases/...` paths in the table.
- **D-03:** Cover the six v1 requirements only. After the table, state that v2
  requirements are deferred and are not part of the v1 traceability table or
  coverage count. Preserve the v1 coverage calculation as 6 total, 6 mapped,
  0 unmapped.
- **D-04:** Use exactly four `Evidence type` values:
  - `Documented scope` — `SCOPE-01`, `SCOPE-04`
  - `Forge implementation inventory` — `SCOPE-02`
  - `Documented exclusions` — `SCOPE-03`
  - `Review and traceability` — `TRACE-01`, `REVIEW-01`
- **D-05:** Use exactly two `Status` values: `In progress` and `Verified`.
  `Verified` means the requirement's deliverable passed its phase
  `VERIFICATION.md`. It does not mean Forge full acceptance, target ABI,
  deployment, or robot-system acceptance.
- **D-06:** `SCOPE-01` through `SCOPE-04` are `Verified` from the passed Phase 1
  and Phase 2 verification reports. `TRACE-01` and `REVIEW-01` start as
  `In progress`; after Phase 3 verification passes, the phase-completion step
  changes them to `Verified` and points them at `03-01-SUMMARY.md` and
  `03-VERIFICATION.md`.
- **D-07:** Update `REQUIREMENTS.md`'s `Last updated` footer after the traceability
  and review work. Do not duplicate the traceability table in `PROJECT.md`,
  `ROADMAP.md`, or `SCOPE.md`.

### Consistency Review

- **D-08:** Review exactly these four baseline documents:
  `PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, and `SCOPE.md`. `STATE.md` is
  operational state, not baseline review scope; phase tracking updates it
  separately.
- **D-09:** Use domain authority to resolve conflicts:
  - `SCOPE.md` — composition, portability, implemented capabilities, exclusions.
  - `REQUIREMENTS.md` — requirement identifiers, statuses, traceability.
  - `ROADMAP.md` — phase assignment, phase goals, plan counts.
  - `PROJECT.md` — project identity, constraints, project-level requirement state.
  Cross-domain conflicts that cannot be resolved by these rules are recorded as
  unresolved with a disposition.
- **D-10:** Use automated structural checks plus manual semantic review.
  Automated checks cover IDs, unique phase assignment, status values, dates,
  links, table headers, counts, and plan/phase counts. Manual review covers
  scope meaning, requirement wording, and cross-document intent.
- **D-11:** Record the review in a separate phase artifact
  `agents/planning/phases/03-baseline-traceability-and-consistency-review/03-REVIEW.md`.
  `SCOPE.md` remains the authoritative baseline; do not add a review section to
  it.
- **D-12:** The review findings table must use exactly these columns:
  `| ID | Severity | Documents | Finding | Disposition | Action/Decision |`.
  - `Severity`: `High`, `Medium`, `Low`.
  - `Disposition`: `Fixed`, `Deferred`, `Accepted`, `Blocked`.
  - Every finding gets an Action/Decision. `Deferred` names the target (for
    example v2 or a later phase); `Blocked` names the owner and next action;
    `Accepted` records the rationale.
- **D-13:** If no inconsistencies are found, say so explicitly after listing the
  checks performed. Do not use an empty findings table as proof of review.

### Fix and Evidence Policy

- **D-14:** Fix mechanical inconsistencies directly using domain authority:
  IDs, phase assignments, statuses, dates, links, counts, plan counts, and table
  headers. Do not change scope meaning, requirements, or phase intent. Record
  semantic or scope conflicts as findings instead of silently rewriting them.
- **D-15:** Use only `Fixed`, `Deferred`, `Accepted`, and `Blocked` as review
  dispositions. No finding may be left without a next action or deferral target.
- **D-16:** Prior phase `VERIFICATION.md` reports are historical evidence. Do not
  re-run prior phase verifiers in this phase. If mechanical fixes change a file
  covered by an earlier verification report, record that the report is stale in
  `03-REVIEW.md`; Phase 3 verification is the current baseline verification. Do
  not describe a stale prior report as fresh.
- **D-17:** Do not modify `documents/` or `forge/`; no Forge acceptance, Docker,
  Bazel, or target tests. Do not modify source code.

### the agent's Discretion

- Exact wording of the review summary and finding descriptions.
- Whether to add a short methodology paragraph before the review tables.
- Table line wrapping and Markdown formatting, provided all required rows,
  columns, statuses, and dispositions remain explicit.
- The order of findings within each severity group.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Baseline Documents Under Review

- `.planning/PROJECT.md` — identity, validated/active requirements, constraints,
  context, and key decisions.
- `.planning/REQUIREMENTS.md` — v1/v2 requirements, exclusions, and the
  traceability table to extend.
- `.planning/ROADMAP.md` — phase goals, requirements, dependencies, plans, and
  progress.
- `.planning/SCOPE.md` — authoritative composition, boundaries, portability,
  implemented Forge capabilities, and explicit exclusions.

### Prior Phase Evidence

- `.planning/phases/01-system-composition-and-portability-boundary/01-01-PLAN.md`
- `.planning/phases/01-system-composition-and-portability-boundary/01-01-SUMMARY.md`
- `.planning/phases/01-system-composition-and-portability-boundary/01-VERIFICATION.md`
- `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-01-PLAN.md`
- `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-01-SUMMARY.md`
- `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-VERIFICATION.md`

### Prior Context and Instructions

- `.planning/phases/01-system-composition-and-portability-boundary/01-CONTEXT.md`
  — locked Phase 1 composition and document-ownership decisions.
- `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-CONTEXT.md`
  — locked Phase 2 capability, exclusion, and evidence decisions.
- `agents/AGENTS.md` — multi-repository ownership, commit boundaries, and
  evidence rules. Root `AGENTS.md` links to this file.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets

- `REQUIREMENTS.md` already contains the six-requirement v1 traceability table;
  Phase 3 extends it rather than creating a parallel table.
- `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` already link to
  `.planning/SCOPE.md`; the review checks those links rather than duplicating the
  baseline.
- Phase 1 and Phase 2 verification reports provide passed evidence for
  `SCOPE-01` through `SCOPE-04`.

### Established Patterns

- One authoritative baseline with link-not-copy references from the project
  planning documents.
- Evidence states are separated into requirements, design, implementation, test
  results, and target acceptance.
- Planning documents use Markdown tables with explicit headers and
  repository-relative paths.
- Phase verification reports record covered files and digests; stale reports are
  not treated as fresh.

### Integration Points

- `REQUIREMENTS.md` gains the full traceability chain and updated coverage.
- `03-REVIEW.md` records findings and dispositions for the four baseline
  documents.
- Phase 3 completion updates requirement statuses and project tracking; it does
  not change the Phase 1/2 scope decisions.

</code_context>

<specifics>
## Specific Ideas

- The traceability table is wide but explicit: seven columns, one row per v1
  requirement.
- `In progress` is the honest status for TRACE-01 and REVIEW-01 until Phase 3's
  own verification passes.
- The review uses domain authority rather than a single global "winning"
  document.
- Mechanical fixes are allowed; semantic rewrites are not.
- A stale prior verification report is recorded as stale, not silently refreshed.

</specifics>

<deferred>
## Deferred Ideas

- Reviewing `STATE.md` as part of the baseline consistency audit.
- Expanding the review to the codebase map and Forge authoritative documents.
- Re-running Phase 1/2 verifiers after mechanical fixes.
- Changing scope meaning or requirements during mechanical fixes.
- Adding a separate TRACEABILITY.md or embedding the review in SCOPE.md.

</deferred>

---

*Phase: 3-Baseline Traceability and Consistency Review*
*Context gathered: 2026-11-04*
