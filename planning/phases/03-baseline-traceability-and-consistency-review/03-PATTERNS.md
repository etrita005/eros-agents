# Phase 3: Baseline Traceability and Consistency Review - Pattern Map

**Mapped:** 2026-11-04
**Files analyzed:** 5 (1 new, 1 definitely modified, 3 conditionally modified)
**Analogs found:** 5 / 5 (1 exact-self, 4 role-match / section-level)

This is a documentation-only phase. The patterns below are Markdown document
structure, table shapes, status and disposition vocabularies, evidence-citation
formatting, and review-report structure. No application code patterns are
relevant or invented.

D-17 forbids modifying `documents/` or `forge/` and forbids source-code changes.
D-08 excludes `STATE.md` from the baseline review scope. This pattern map is
read-only except for this file.

## File Classification

| New/Modified File | Role | Data Flow | Closest Analog | Match Quality |
|-------------------|------|-----------|----------------|---------------|
| `agents/planning/REQUIREMENTS.md` (modified) | planning document / requirements registry and traceability | transform (six v1 requirements + phase evidence into a seven-column chain) | itself, `REQUIREMENTS.md:57-73`; `01-VERIFICATION.md:130-137` and `02-VERIFICATION.md:88-95` Requirements Coverage; `01-VERIFICATION.md:6-12` covered files | exact (self) for section placement; role-match for the expanded chain |
| `agents/planning/phases/03-baseline-traceability-and-consistency-review/03-REVIEW.md` (new) | phase artifact / consistency review report | audit (four baseline documents into findings and dispositions) | `02-VERIFICATION.md` report structure; `01-SECURITY.md:26-49` and `02-SECURITY.md:27-51` threat register; `CONCERNS.md:7-24` finding blocks | role-match; no exact self |
| `agents/planning/PROJECT.md` (conditionally modified) | planning document / project overview | transform | itself, `PROJECT.md:17-48`, `93-101`, `120-121` | exact (self) |
| `agents/planning/ROADMAP.md` (conditionally modified) | planning document / roadmap and progress | transform | itself, `ROADMAP.md:31-32`, `56`, `63-67` | exact (self) |
| `agents/planning/SCOPE.md` (conditionally modified) | planning document / authoritative scope baseline | transform | itself, `SCOPE.md:1-4`, `102-105` | exact (self) |

The three baseline documents are modified only for mechanical fixes under D-14
and, after Phase 3 verification passes, for phase-completion bookkeeping.
Semantic or scope conflicts are recorded in `03-REVIEW.md` instead of silently
rewritten (D-14, D-15). `03-REVIEW.md` is new and is the only new deliverable.
`STATE.md` is not a review target; phase tracking updates it separately (D-08).

## Pattern Assignments

### `agents/planning/REQUIREMENTS.md` (planning document, transform)

**Analog:** itself, plus the verification reports' Requirements Coverage table
and frontmatter `covered_files` list.

#### Document metadata and footer pattern

**Analog:** `REQUIREMENTS.md:1-4` and `REQUIREMENTS.md:75-77`.

```markdown
# Requirements: EROS

**Defined:** 2026-11-03
**Core Value:** Provide a unified, verifiable software foundation for robot hardware across supported platforms while keeping build and delivery reproducible.
```

```markdown
---
*Requirements defined: 2026-11-03*
*Last updated: 2026-11-03 after roadmap creation*
```

Keep the existing title, `**Defined:**`, `**Core Value:**`, and the two-line
footer shape. D-07 requires updating the `Last updated` line after the
traceability and review work. Match the existing wording pattern and put the
change reason after the date, for example:

```markdown
*Last updated: 2026-11-04 after baseline traceability and consistency review*
```

Do not change `**Defined:**` and do not add a second metadata block.

#### Existing traceability section and coverage block

**Analog:** `REQUIREMENTS.md:57-73`.

```markdown
## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| SCOPE-01 | Phase 1 | Complete |
| SCOPE-02 | Phase 2 | Complete |
| SCOPE-03 | Phase 2 | Complete |
| SCOPE-04 | Phase 1 | Complete |
| TRACE-01 | Phase 3 | Pending |
| REVIEW-01 | Phase 3 | Pending |

**Coverage:**
- v1 requirements: 6 total
- Mapped to phases: 6
- Unmapped: 0
```

D-01 requires extending this section in place; do not create a separate
traceability file. Keep the `## Traceability` heading and the six-row scope.
Replace the three-column table rather than adding a second table. Preserve the
`**Coverage:**` block verbatim:

```markdown
**Coverage:**
- v1 requirements: 6 total
- Mapped to phases: 6
- Unmapped: 0
```

The counts must remain 6 total, 6 mapped, 0 unmapped (D-03). Do not add v2 rows
to the table or to these counts.

#### Required seven-column table

**Analog for one-row-per-requirement and plan/evidence cells:**
`01-VERIFICATION.md:132-135` and `02-VERIFICATION.md:90-93`.

```markdown
| Requirement | Source Plan | Description | Status | Evidence |
|-------------|-------------|-------------|--------|----------|
| SCOPE-01 | `01-01-PLAN.md` | Define the long-term EROS system composition and the responsibility of each composition area. | ✓ SATISFIED | `SCOPE.md` System Composition (`14-28`), Area Responsibilities (`30-38`), and Adjacent-Area Boundaries (`40-47`); `REQUIREMENTS.md:13,63` marks it Complete and maps it to Phase 1; `PROJECT.md:32-33` records it as validated in Phase 1. |
```

This analog already uses one row per requirement, a backticked plan reference,
a status column, and a multi-artifact evidence cell. D-01 changes the column
order and adds Summary, Verification, and Evidence type; copy the row
granularity and evidence discipline, not the column order or the `✓ SATISFIED`
vocabulary.

Use exactly this header row from D-01:

```markdown
| Requirement | Phase | Plan | Summary | Verification | Evidence type | Status |
|-------------|-------|------|---------|--------------|---------------|--------|
```

Use a padded separator row and keep one physical row per requirement. The table
is intentionally wide; wrap cell text only when necessary and keep each row a
valid Markdown table row.

#### Row mapping and path formatting

**Analog for `.planning/phases/...` path formatting:**
`01-VERIFICATION.md:6-12` and `02-VERIFICATION.md:6-10`.

```yaml
covered_files:
  - ".planning/phases/01-system-composition-and-portability-boundary/01-01-PLAN.md"
  - ".planning/phases/01-system-composition-and-portability-boundary/01-01-SUMMARY.md"
  - "agents/planning/PROJECT.md"
  - "agents/planning/REQUIREMENTS.md"
  - "agents/planning/ROADMAP.md"
  - "agents/planning/SCOPE.md"
```

D-02 requires repository-relative `.planning/phases/...` paths in the table.
Use the navigation path consistently and wrap each path in backticks. Do not
alternate between `.planning/...` and `agents/planning/...` within the table.

Suggested row content, anchored to existing phase filenames and D-04/D-06:

| Requirement | Phase | Plan | Summary | Verification | Evidence type | Status |
|-------------|-------|------|---------|--------------|---------------|--------|
| SCOPE-01 | Phase 1 | `.planning/phases/01-system-composition-and-portability-boundary/01-01-PLAN.md` | `.planning/phases/01-system-composition-and-portability-boundary/01-01-SUMMARY.md` | `.planning/phases/01-system-composition-and-portability-boundary/01-VERIFICATION.md` | Documented scope | Verified |
| SCOPE-04 | Phase 1 | `.planning/phases/01-system-composition-and-portability-boundary/01-01-PLAN.md` | `.planning/phases/01-system-composition-and-portability-boundary/01-01-SUMMARY.md` | `.planning/phases/01-system-composition-and-portability-boundary/01-VERIFICATION.md` | Documented scope | Verified |
| SCOPE-02 | Phase 2 | `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-01-PLAN.md` | `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-01-SUMMARY.md` | `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-VERIFICATION.md` | Forge implementation inventory | Verified |
| SCOPE-03 | Phase 2 | `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-01-PLAN.md` | `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-01-SUMMARY.md` | `.planning/phases/02-implemented-scope-and-explicit-exclusions/02-VERIFICATION.md` | Documented exclusions | Verified |
| TRACE-01 | Phase 3 | `.planning/phases/03-baseline-traceability-and-consistency-review/03-01-PLAN.md` | `.planning/phases/03-baseline-traceability-and-consistency-review/03-01-SUMMARY.md` | `.planning/phases/03-baseline-traceability-and-consistency-review/03-VERIFICATION.md` | Review and traceability | In progress |
| REVIEW-01 | Phase 3 | `.planning/phases/03-baseline-traceability-and-consistency-review/03-01-PLAN.md` | `.planning/phases/03-baseline-traceability-and-consistency-review/03-01-SUMMARY.md` | `.planning/phases/03-baseline-traceability-and-consistency-review/03-VERIFICATION.md` | Review and traceability | In progress |

Rules for those cells:

- `Requirement` is the exact requirement ID from `REQUIREMENTS.md:13-21`; do not
  renumber or reword it.
- `Phase` uses the existing `Phase 1` / `Phase 2` / `Phase 3` form from
  `REQUIREMENTS.md:61-68` and `ROADMAP.md:24,37,49`.
- `Plan`, `Summary`, and `Verification` are backticked `.planning/phases/...`
  paths. Phase 1 and Phase 2 paths already exist. Phase 3 paths are forward
  references until the planner/executor creates them; record the expected path
  and mark the row `In progress`. At phase completion, confirm the actual
  `03-01-SUMMARY.md` and `03-VERIFICATION.md` paths and change the status to
  `Verified` (D-06).
- `Evidence type` uses only the four D-04 values, exactly as written:
  `Documented scope`, `Forge implementation inventory`, `Documented exclusions`,
  `Review and traceability`. Use the D-04 mapping; do not invent a fifth value.
- `Status` uses only `In progress` or `Verified` (D-05). Do not use
  `✓ SATISFIED` or `PASS` from the verification vocabulary, and do not carry
  forward the old `Complete` / `Pending` values from the existing table.

#### v2 deferral note and evidence-status guard

**Analog for deferral wording:** `REQUIREMENTS.md:25` and `SCOPE.md:99`.

```markdown
Deferred to future milestones. Tracked but not in the current roadmap.
```

```markdown
- System update and OTA are deferred to v2. This baseline states only that
  updates cross the runtime, BSP, and firmware areas and does not assign update
  orchestration, boot-slot, or firmware-recovery ownership.
```

After the table, state the v2 boundary explicitly (D-03), for example:

```markdown
v2 requirements are deferred. They are not part of this v1 traceability table
or the v1 coverage count.
```

If the planner needs the v2 identifiers for clarity, cite the existing
`## v2 Requirements` section rather than duplicating its rows. Do not add v2
rows to the table.

Add the D-05 evidence-status guard after the table so `Verified` is not read as
system acceptance. The wording pattern comes from `SCOPE.md:10-12` and
`agents/AGENTS.md:53-62`:

```markdown
`Verified` means the requirement's deliverable passed its phase `VERIFICATION.md`.
It does not mean Forge full acceptance, target ABI compatibility, deployment, or
robot-system acceptance.
```

Keep `TRACE-01` and `REVIEW-01` as `In progress` until Phase 3 verification
passes; only the phase-completion step changes them to `Verified` and confirms
their Summary/Verification paths (D-06). Checkboxes at
`REQUIREMENTS.md:20-21` stay unchecked until the same point; the SCOPE-01
through SCOPE-04 checkboxes at `REQUIREMENTS.md:13-16` are already checked and
must remain checked.

---

### `agents/planning/phases/03-baseline-traceability-and-consistency-review/03-REVIEW.md` (phase artifact, audit)

**Analog:** `02-VERIFICATION.md` for report structure and evidence discipline;
`01-SECURITY.md` and `02-SECURITY.md` for an ID / Severity / Disposition
register; `CONCERNS.md` for actionable finding blocks.

#### File header and metadata

**Analog:** `02-VERIFICATION.md:1-24` and `01-VERIFICATION.md:28-33`.

```markdown
---
phase: 02-implemented-scope-and-explicit-exclusions
verified: 2026-11-04T01:16:25Z
status: passed
score: 11/11 must-haves verified
covered_files:
  - ".planning/phases/02-implemented-scope-and-explicit-exclusions/02-01-PLAN.md"
  - ".planning/phases/02-implemented-scope-and-explicit-exclusions/02-01-SUMMARY.md"
  - "agents/planning/SCOPE.md"
covered_digest: "v4:sha256:b77de757159cb10f73afeec9cefc12c944b64e9cfb54e124ce7bfa024aedaa49"
behavior_unverified: 0
overrides_applied: 0
decision_coverage:
  honored: 15
  total: 15
  not_honored: []
---

# Phase 2: Implemented Scope and Explicit Exclusions Verification Report

**Phase Goal:** Readers can distinguish Forge's implemented capabilities from unimplemented EROS system scope, and the baseline's exclusions are explicit and reasoned.
**Verified:** 2026-11-04T01:16:25Z
**Status:** passed
**Re-verification:** No — initial verification
```

The review is a phase artifact but not a verification report. Use a minimal
frontmatter that records the phase, review date, and documents reviewed, and
omit verification-only fields such as `status: passed`, `score`, and
`covered_digest`:

```yaml
---
phase: 03-baseline-traceability-and-consistency-review
reviewed: 2026-11-04
documents:
  - ".planning/PROJECT.md"
  - ".planning/REQUIREMENTS.md"
  - ".planning/ROADMAP.md"
  - ".planning/SCOPE.md"
---
```

Then use the established report header shape:

```markdown
# Phase 3: Baseline Traceability and Consistency Review Report

**Reviewed:** 2026-11-04
**Baseline:** `.planning/SCOPE.md`
**Documents reviewed:** `.planning/PROJECT.md`, `.planning/REQUIREMENTS.md`, `.planning/ROADMAP.md`, `.planning/SCOPE.md`
**Scope:** Planning documents only; no implementation, Forge acceptance, target ABI, deployment, or robot-system acceptance is claimed.
```

Do not call the review `passed` or `verified`. Those are phase-verification
outcomes owned by `03-VERIFICATION.md`; the review report records findings and
dispositions.

#### Review scope and authority table

**Analog:** the `Area | Layer | Responsibility | Boundary` table at
`SCOPE.md:32-38` and the `Component | Responsibility | File` table at
`ARCHITECTURE.md:41-54` for the two-column authority summary.

D-09 defines domain authority. Record it as a small table so the review method
is auditable:

```markdown
| Document | Authority |
|----------|-----------|
| `.planning/SCOPE.md` | Composition, portability, implemented capabilities, exclusions |
| `.planning/REQUIREMENTS.md` | Requirement identifiers, statuses, traceability |
| `.planning/ROADMAP.md` | Phase assignment, phase goals, plan counts |
| `.planning/PROJECT.md` | Project identity, constraints, project-level requirement state |
```

Scope note: D-08 requires exactly four documents. `REQUIREMENTS.md:21`,
`PROJECT.md:47-48`, and `ROADMAP.md:53` describe the review as covering
`PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` only. The review must use the
D-08 four-document scope. If the narrower requirement wording is treated as an
inconsistency, record it as a finding; D-14 forbids silently rewriting the
requirement text. Do not add `STATE.md`, the codebase map, `documents/`, or
`forge/documents/` to the review.

#### Review method and checks performed

**Analog:** `01-VALIDATION.md:16-24` for the `Property | Value` method table and
`01-VERIFICATION.md:146-157` for a numbered check-results table.

```markdown
## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | none — documentation-only phase; structural assertions use shell grep/test |
| **Config file** | none |
| **Quick run command** | `grep -Fqx '# EROS Scope Baseline' agents/planning/SCOPE.md` |
| **Full suite command** | `git -C agents diff --check` plus the Task 3 structure/link suite in `01-01-PLAN.md` |
```

```markdown
### Phase-Specific Check Results

| # | Check | Result | Evidence |
|---|-------|--------|----------|
| 1 | SCOPE-01: two layers, exactly five areas, one responsibility and explicit boundary per area, Forge separated from the robot OS product, and Linux/Zephyr/fieldbus ownership | PASS | `SCOPE.md:16-28,32-38,44-45`; five-row table verified structurally. |
```

Use the same numbered-check shape, but adapt the result vocabulary to a review.
D-10 splits checks into automated structural checks and manual semantic review.
A suggested structure:

```markdown
## Review Method

Automated structural checks cover identifiers, unique phase assignment, status
values, dates, links, table headers, counts, and plan/phase counts. Manual
review covers scope meaning, requirement wording, and cross-document intent.

## Checks Performed

| # | Check | Type | Documents | Result | Evidence |
|---|-------|------|-----------|--------|----------|
| 1 | Six v1 requirement IDs are unique and match the v1 registry | Automated | `REQUIREMENTS.md`, `PROJECT.md`, `ROADMAP.md` | Clear | `REQUIREMENTS.md:13-21`; `PROJECT.md:45-48`; `ROADMAP.md:49` |
| 2 | Each v1 requirement maps to exactly one phase | Automated | `REQUIREMENTS.md`, `ROADMAP.md` | Clear | `REQUIREMENTS.md:61-68`; `ROADMAP.md:24,37,49` |
| 3 | Status values are limited to `In progress` / `Verified` and agree with checkboxes and Validated/Active lists | Automated | `REQUIREMENTS.md`, `PROJECT.md`, `ROADMAP.md` | Clear | `REQUIREMENTS.md:13-21,61-68`; `PROJECT.md:32-48`; `ROADMAP.md:65-67` |
| 4 | Dates are consistent across the four documents | Automated | all four | Clear | `REQUIREMENTS.md:77`; `PROJECT.md:121`; `SCOPE.md:3-4,104-105`; `ROADMAP.md:15-16,65-66` |
| 5 | Baseline links resolve and are not duplicated as tables | Automated | all four | Clear | `.planning/SCOPE.md` targets; `PROJECT.md:78`; `REQUIREMENTS.md:9`; `ROADMAP.md:25` |
| 6 | Table headers match D-01/D-12 and each traceability/review header appears once | Automated | `REQUIREMENTS.md`, `ROADMAP.md` | Clear | Header-row counts across the four documents |
| 7 | Coverage counts are 6 total / 6 mapped / 0 unmapped and match the table rows | Automated | `REQUIREMENTS.md` | Clear | `REQUIREMENTS.md:70-73` |
| 8 | Phase and plan counts match `ROADMAP.md` and the phase directories | Automated | `ROADMAP.md`, phase directories | Clear | `ROADMAP.md:31-32,43-44,56,65-67`; `ls .planning/phases/*` |
| 9 | Scope statements agree on composition, portability, implemented capabilities, and exclusions | Manual | all four | Clear | Read `SCOPE.md:14-89` against `PROJECT.md:5-8,32-41` and `ROADMAP.md:5` |
| 10 | Requirement wording and identifiers agree across documents | Manual | `REQUIREMENTS.md`, `PROJECT.md`, `ROADMAP.md` | Clear | Read `REQUIREMENTS.md:13-21` against `PROJECT.md:45-48` and `ROADMAP.md:49` |
| 11 | No excluded capability is described as delivered, selected, verified, or accepted | Manual | all four | Clear | `SCOPE.md:73-89`; `PROJECT.md:50-64`; `REQUIREMENTS.md:44-55` |
```

Rules for the checks table:

- `Type` is exactly `Automated` or `Manual`, matching D-10.
- Use `Clear` or `Finding` (optionally `Finding R-01`) as the result. Do not use
  `PASS` / `FAIL` / `SKIP` / `BLOCKED`; those are Forge acceptance-runner states,
  not baseline-review results.
- `Evidence` cells use backticked `FILE:line` or `FILE:line-line` references and
  backticked commands, following the verification-report Evidence style at
  `01-VERIFICATION.md:132-135` and `02-VERIFICATION.md:90-93`.
- List every check actually performed. D-13 requires the no-inconsistency
  statement to follow the listed checks, not replace them.

#### Findings table

**Analog for ID / Severity / Disposition / action columns:**
`01-SECURITY.md:28-39` and `02-SECURITY.md:29-41`.

```markdown
| Threat ID | Category | Component | Severity | Disposition | Mitigation | Status |
|-----------|----------|-----------|----------|-------------|------------|--------|
| T-01-01 | Tampering | `agents/planning/SCOPE.md` normative content | high | mitigate | Task 1 specified exact area names, responsibilities, boundaries, portability rows, and D-01 through D-14 literals; Task 3 re-checked every required literal/heading and the five-row responsibility table. Validated in commit `a84e90e`. | closed |
```

```markdown
*Status: open · closed · open — below high threshold (non-blocking)*
*Severity: critical > high > medium > low — only open threats at or above workflow.security_block_on count toward threats_open*
*Disposition: mitigate (implementation required) · accept (documented risk) · transfer (third-party)*
```

This is the closest existing register with one row per item, a stable ID, a
severity, a disposition, and an action/evidence cell. Copy the shape, not the
security vocabulary: the review uses D-12's severity and disposition values.

**Analog for actionable finding wording:** `CONCERNS.md:7-12`.

```markdown
**Acceptance registry does not cover the complete release contract:**
- Issue: The documented environment/entry/CI cases are not all executable registry entries. Several registered external scopes are unconditional `blocked_case(...)` placeholders rather than environment adapters.
- Files: `forge/tests/acceptance_cases.py:959`, `forge/tests/test_runner.py`, `forge/documents/test-design.md:5`, `forge/documents/technical-design.md:320`.
- Impact: Neither a successful local build nor `--all --strict` establishes complete release acceptance. Merely supplying a target or cache service cannot execute a placeholder with no check implementation.
- Fix approach: Implement external execution/evidence adapters, register TC-ENV-05..07, TC-ENTRY-01..05 and TC-CI-03, and add a registry-versus-design coverage check. Preserve PASS/FAIL/SKIP/BLOCKED distinctions and report completeness checks.
- Evidence boundary: This is an explicit implementation/coverage gap, not a claim that existing local assertions fail.
```

Use the `Issue` / `Files` / `Impact` / `Fix approach` discipline inside the
`Finding` and `Action/Decision` cells: state the concrete conflict, cite the
files and lines, state the impact, and give an actionable next step.

Use exactly this header row from D-12:

```markdown
| ID | Severity | Documents | Finding | Disposition | Action/Decision |
|----|----------|-----------|---------|-------------|-----------------|
```

Row template:

```markdown
| R-01 | High | `.planning/REQUIREMENTS.md`, `.planning/ROADMAP.md` | `REQUIREMENTS.md:67` records `<state A>` while `ROADMAP.md:49` records `<state B>`; the two statements assign different `<identifier/phase/status>`. | Fixed | Updated `<file>:<lines>` to `<corrected state>`; commit `<hash>`. |
```

Value rules:

- `ID`: use a stable sequential review ID such as `R-01`, `R-02`. The corpus
  uses prefixed IDs (`SCOPE-01`, `T-01-01`, `AR-01-01`); there is no existing
  review-ID convention. Do not use a bare number.
- `Severity`: exactly `High`, `Medium`, `Low` as capitalized in D-12. Do not
  use the lowercase security severities (`high`, `medium`, `low`) or the
  verification `Info` label. D-12 is authoritative.
- `Documents`: backticked `.planning/...` paths, comma-separated. List every
  document implicated in the finding; use `.planning/PROJECT.md`,
  `.planning/REQUIREMENTS.md`, `.planning/ROADMAP.md`, and `.planning/SCOPE.md`.
- `Finding`: one or two sentences. State the concrete inconsistency and cite at
  least one `FILE:line` or `FILE:line-line` reference. If the evidence is a
  command result, name the command and its result. Do not write vague findings
  such as "documents differ".
- `Disposition`: exactly `Fixed`, `Deferred`, `Accepted`, or `Blocked`
  (D-12, D-15). Do not import `mitigate` / `accept` / `transfer` from the
  security register.
- `Action/Decision`: required for every finding (D-12, D-15).
  - `Fixed`: state what changed and where, with a commit hash if available.
  - `Deferred`: name the target, for example `v2` or a named later phase.
  - `Accepted`: record the rationale for accepting the inconsistency.
  - `Blocked`: name the owner and the next action.
  No finding may end with an empty action or an unowned "investigate later".

If the same mechanical change resolves several findings, repeat the action text
or add a `## Fix Log` table patterned on `01-VERIFICATION.md:159-168`
(`Claim | Evidence | Result`) with columns `Finding | File | Change | Commit`.
Do not leave a finding unlinked from the change that resolved it.

If no inconsistencies are found, do not write an empty findings table. After
the checks table, state explicitly (D-13):

```markdown
No inconsistencies were found in the four baseline documents after the checks
listed above.
```

Then still include the summary counts and the sign-off below.

#### Summary and prior-verification impact

**Analog:** `02-VERIFICATION.md:122-126` and `01-VERIFICATION.md:178-180`.

```markdown
### Gaps Summary

None. The phase goal is achieved in the codebase, not merely in the summary: ...
All 27 plan validation checks, 2/2 artifact checks, 5/5 key links, and 15/15
decision-coverage checks pass.

One expected cross-phase lifecycle observation, not a Phase 2 gap:
`verification.status` for Phase 1 now reports `stale` because Phase 2
intentionally changed `SCOPE.md`, which is one of Phase 1's covered files.
Phase 1 remains historically verified; its report is not re-opened by this
phase.
```

Use the same explicit "None." style and the same historical-evidence
discipline. A suggested summary:

```markdown
## Summary

| Metric | Value |
|--------|-------|
| Documents reviewed | 4 |
| Automated checks | N/N clear |
| Manual checks | N/N clear |
| Findings | N |
| High / Medium / Low | N / N / N |
| Fixed / Deferred / Accepted / Blocked | N / N / N / N |
| Findings without disposition or action | 0 |
```

After the table, state whether unresolved items remain. If all findings are
dispositioned, say so. If any are `Deferred` or `Blocked`, name the target or
owner here as well as in the findings row.

D-16 requires a prior-verification-impact statement if a mechanical fix changes
a file covered by an earlier report. Model it on the `02-VERIFICATION.md:126`
paragraph:

```markdown
## Prior Verification Impact

`01-VERIFICATION.md` and `02-VERIFICATION.md` are historical evidence and were
not re-run in this phase. `<report>` now reports `stale` because Phase 3 changed
`<covered file>`. This is recorded, not treated as a fresh verification.
`03-VERIFICATION.md` is the current baseline verification.
```

If no prior covered file changed, state that the prior reports remain unaffected.
Do not describe a stale report as fresh and do not re-run the Phase 1/2
verifiers (D-16).

A short sign-off block is optional but matches the phase-artifact convention at
`01-VALIDATION.md:66-75` and `01-SECURITY.md:61-68`:

```markdown
## Sign-Off

- [x] All four baseline documents reviewed
- [x] All findings have a disposition and an Action/Decision
- [x] No scope meaning or requirement wording was changed mechanically
- [x] Prior verification reports are recorded as historical evidence
```

---

### `agents/planning/PROJECT.md` (conditionally modified, transform)

**Analog:** itself.

**Validated / Active pattern** (`PROJECT.md:17-48`):

```markdown
### Validated

<!-- Existing capabilities established by the codebase map. These are
subproject capabilities, not EROS system-level acceptance. -->

- ✓ Defined the long-term EROS system composition and the responsibility of each
  composition area; see `.planning/SCOPE.md` — Phase 1.
...
### Active

- [ ] **TRACE-01**: Establish requirement-to-phase traceability with evidence
  status for the baseline.
- [ ] **REVIEW-01**: Review `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` for
  consistency and record unresolved items.
```

If Phase 3 completes, follow the Phase 1/2 precedent and move TRACE-01 and
REVIEW-01 into `Validated` with a `— Phase 3` marker and a reference to
`.planning/REQUIREMENTS.md` / `.planning/phases/03-.../03-VERIFICATION.md`.
Do not do this before Phase 3 verification passes. Preserve the HTML comment
that distinguishes subproject capabilities from EROS system acceptance.

**Key Decisions pattern** (`PROJECT.md:93-101`):

```markdown
| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Initialize GSD at the EROS workspace root | Cross-repository scope and subproject boundaries need one project-level baseline | — Pending |
| Keep Forge as a subproject, not the EROS roadmap | Forge is build and delivery infrastructure, not the whole robot operating system | — Pending |
```

A review may correct a mechanical inconsistency in the `Outcome` cell only when
the evidence supports it. Do not rewrite a decision or rationale to resolve a
semantic conflict; record that conflict in `03-REVIEW.md` (D-14). Keep
`— Pending` where no outcome evidence exists.

**Footer pattern** (`PROJECT.md:120-121`):

```markdown
---
*Last updated: 2026-11-04 after Phase 2*
```

Update the date and reason together if this file changes.

---

### `agents/planning/ROADMAP.md` (conditionally modified, transform)

**Analog:** itself.

**Phase plan list** (`ROADMAP.md:31-32`):

```markdown
**Plans**: 1/1 plans complete
- [x] `01-01-PLAN.md` — Create the scope baseline and link it from PROJECT.md, REQUIREMENTS.md, and ROADMAP.md
```

**Phase 3 placeholder to replace at planning time** (`ROADMAP.md:56`):

```markdown
**Plans**: TBD
```

**Progress table** (`ROADMAP.md:63-67`):

```markdown
| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. System Composition and Portability Boundary | 1/1 | Complete    | 2026-11-04 |
| 2. Implemented Scope and Explicit Exclusions | 1/1 | Complete    | 2026-11-04 |
| 3. Baseline Traceability and Consistency Review | 0/TBD | Not started | - |
```

Mechanical review fixes may align plan counts, phase labels, statuses, dates,
and table headers. Do not change Phase 3 success criteria or phase intent to
match a finding; record semantic conflicts in `03-REVIEW.md`. Phase completion
updates the Phase 3 plan count and progress row once `03-01-PLAN.md` and
`03-VERIFICATION.md` exist. Keep the `**Success Criteria** (what must be TRUE):`
wording and the numbered criteria.

---

### `agents/planning/SCOPE.md` (conditionally modified, transform)

**Analog:** itself.

**Metadata and footer** (`SCOPE.md:1-4`, `SCOPE.md:102-105`):

```markdown
# EROS Scope Baseline

**Defined:** 2026-11-03
**Last updated:** 2026-11-04
```

```markdown
---
*Scope baseline defined: 2026-11-03*
*Last updated: 2026-11-04*
```

SCOPE.md remains the authoritative baseline. D-11 forbids adding a review
section to it. Mechanical fixes may correct dates, links, counts, and table
headers only; do not change scope meaning, composition, portability, capability
boundaries, or exclusion semantics (D-14). Record semantic or scope conflicts
in `03-REVIEW.md`.

**Evidence guard to preserve** (`SCOPE.md:60`):

```markdown
This table records implementation presence, not test success, deployment, or target acceptance. Evidence cites a repository-relative code path and the authoritative Forge document that defines or describes the mechanism; a design document, recipe, example, or unexecuted check is not a passing result.
```

Do not weaken this guard or any "not established" / "not verified" boundary
while making mechanical fixes.

## Shared Patterns

### Single source of truth via links

**Source:** D-07 and D-11; `PROJECT.md:78`; `REQUIREMENTS.md:9`;
`ROADMAP.md:25`; Phase 1 `01-PATTERNS.md:549-558`.

`SCOPE.md` owns composition, portability, implemented capabilities, and
exclusions. `REQUIREMENTS.md` owns requirement IDs, statuses, and the
traceability table. `ROADMAP.md` owns phase assignment, goals, and plan counts.
`PROJECT.md` owns project identity, constraints, and project-level requirement
state. Do not duplicate the traceability table in the other three documents;
link to the authoritative section or cite its path.

### Table conventions

**Source:** `REQUIREMENTS.md:61-68`; `SCOPE.md:32-56`; `ROADMAP.md:63-67`;
`01-VERIFICATION.md:132-135`; `02-SECURITY.md:29-41`.

Use a header row, a separator row, and one record per row. Keep identifier-like
columns first when they exist. Match the nearest document's separator style
rather than reformatting the file: `REQUIREMENTS.md` uses a compact separator
row, while `SCOPE.md` and `ROADMAP.md` use padded separators. The D-01 and D-12
header rows are exact and must not be reordered or renamed. Do not merge rows
or use nested tables to shorten a wide review table.

### Evidence citation format

**Source:** `01-VERIFICATION.md:132-135`; `02-VERIFICATION.md:90-93`;
`01-PATTERNS.md:570-579`; `AGENTS.md:64-74`.

Use backticked workspace-relative paths. For traceability and review evidence,
prefer the navigation path `.planning/...`; for phase artifacts, use
`.planning/phases/<phase-directory>/<artifact>.md`. Cite line evidence as
`FILE:line` or `FILE:line-line` (for example `REQUIREMENTS.md:61-68` or
`SCOPE.md:16-28`). Do not use relative Markdown links for internal planning
references; that style belongs to `forge/documents/`, a different repository.
Commands may be cited in backticks, but a command is not execution evidence by
itself; record its observed result.

### Vocabulary separation

**Source:** D-04, D-05, D-12; `REQUIREMENTS.md:61-68`; `SCOPE.md:77`;
`01-SECURITY.md:38-39`; `02-SECURITY.md:40-41`; `AGENTS.md:53-62`.

Each table uses its own closed vocabulary. Do not mix them:

| Domain | Allowed values | Do not import |
|--------|----------------|---------------|
| Traceability `Status` | `In progress`, `Verified` | `Complete`, `Pending`, `PASS`, `SATISFIED` |
| Traceability `Evidence type` | `Documented scope`, `Forge implementation inventory`, `Documented exclusions`, `Review and traceability` | Free text, a fifth type |
| Review `Severity` | `High`, `Medium`, `Low` | lowercase `high`/`medium`/`low`, `Info`, `Blocker` |
| Review `Disposition` | `Fixed`, `Deferred`, `Accepted`, `Blocked` | `mitigate`, `accept`, `transfer`, `Resolved`, `Open` |
| Exclusion status (existing SCOPE.md table) | `Not implemented`, `Not selected`, `Not verified`, `Deferred` | Forge acceptance states |
| Forge acceptance (existing Forge docs) | `PASS`, `FAIL`, `SKIP`, `BLOCKED` | Review or traceability statuses |

`Verified` means the requirement's deliverable passed its phase
`VERIFICATION.md`; it does not mean Forge full acceptance, target ABI,
deployment, or robot-system acceptance (D-05). The review's `Accepted` is not
the security register's `accept`; use the D-12 spelling and meaning.

### Evidence boundaries

**Source:** `agents/AGENTS.md:53-62`; `SCOPE.md:10-12,60`;
`01-VERIFICATION.md:107-109`; `02-VERIFICATION.md:65-67`.

This phase produces planning and review artifacts only. Do not claim runtime,
BSP, firmware, Forge, CI/CD, deployment, target ABI, or robot-system
acceptance. State the actual environment, target, checks, and limitations.
A review finding is evidence about document consistency, not implementation
evidence. A prior `VERIFICATION.md` is historical evidence, not a fresh result
(D-16).

### Dates, identifiers, and metadata

**Source:** `REQUIREMENTS.md:75-77`; `PROJECT.md:120-121`;
`SCOPE.md:1-4,102-105`; `ROADMAP.md:15-16,65-66`;
`REQUIREMENTS.md:13-21`.

Use ISO dates (`2026-11-04`). Update the document's own footer or metadata when
the file changes, and keep the defined-date line unchanged. Preserve requirement
IDs and phase/plan filenames exactly: `SCOPE-01`, `SCOPE-02`, `SCOPE-03`,
`SCOPE-04`, `TRACE-01`, `REVIEW-01`; `01-01-PLAN.md`, `01-01-SUMMARY.md`,
`01-VERIFICATION.md`; `02-01-*`, `02-VERIFICATION.md`; `03-01-PLAN.md`,
`03-01-SUMMARY.md`, `03-VERIFICATION.md`. A new review finding ID is sequential
and prefixed (`R-01`, `R-02`); do not reuse a requirement or threat ID.

### Review report structure

**Source:** `02-VERIFICATION.md:19-126`; `01-VERIFICATION.md:28-180`;
`01-SECURITY.md:26-49`; `02-SECURITY.md:27-51`; `CONCERNS.md:7-24`.

Use the phase-artifact sequence: metadata, title, scope/method, checks
performed, findings, summary, prior-verification impact, optional sign-off.
State the checks before the findings. If there are no findings, write the
explicit D-13 sentence after the check table; never use an empty findings table
as proof of review. Every finding row must have a disposition and an
Action/Decision (D-15). Record stale prior reports as historical evidence, not
as fresh verification.

### Language and formatting

**Source:** `REQUIREMENTS.md`, `PROJECT.md`, `ROADMAP.md`, `SCOPE.md`;
Phase 1 `01-PATTERNS.md:626-633`.

Write in English to match the planning corpus. Use unnumbered `##` sections and
`###` subsections as the planning documents do. Do not copy Chinese prose or
numbered heading conventions from `forge/documents/`. Keep existing HTML
comments and untouched sections intact. Use plain severity/disposition text in
the review table; no emoji or icon substitutes.

## No Analog Found

| Item | Role | Data Flow | Reason |
|------|------|-----------|--------|
| Seven-column traceability chain with `Plan`, `Summary`, `Verification`, and `Evidence type` | planning table | transform | No existing table combines requirement, phase, plan, summary, verification, evidence type, and status. Compose from the three-column traceability table, the five-column Requirements Coverage table, and the `covered_files` path convention. Source content is D-01 through D-06. |
| Four-value `Evidence type` vocabulary | planning convention | transform | No existing planning document uses this vocabulary; D-04 defines it. |
| Two-value traceability status vocabulary (`In progress`, `Verified`) | planning convention | transform | Existing traceability uses `Complete` / `Pending`; D-05 replaces it for v1 rows. |
| Six-column review findings table with `Disposition` and `Action/Decision` | phase artifact table | audit | The security threat register has an ID/severity/disposition shape but different columns and vocabulary. The verification Anti-Patterns table has severity but no ID, disposition, or action. Source content is D-12 and D-15. |
| Capitalized `High` / `Medium` / `Low` review severity | review convention | audit | Existing security severities are lowercase; verification uses `Info`. D-12 defines the capitalized review values. |
| `Fixed` / `Deferred` / `Accepted` / `Blocked` disposition vocabulary | review convention | audit | Existing disposition-like vocabularies are security mitigations (`mitigate` / `accept` / `transfer`) and verification results (`VERIFIED` / `WIRED` / `MATCH`). D-12 defines the review set. |
| Automated-plus-manual baseline consistency check inventory | review report section | audit | No existing artifact lists structural and semantic checks over the four baseline documents. Compose from the VALIDATION per-task map (`01-VALIDATION.md:37-45`) and the Phase-Specific Check Results table (`01-VERIFICATION.md:146-157`). D-10 defines the split. |

## Metadata

**Analog search scope:** `agents/planning/` (root planning documents and
`STATE.md`), `agents/planning/phases/01-system-composition-and-portability-boundary/`,
`agents/planning/phases/02-implemented-scope-and-explicit-exclusions/`,
`agents/planning/phases/03-baseline-traceability-and-consistency-review/`,
`agents/planning/codebase/`, and `agents/AGENTS.md`.

**Files scanned:** 20 — `03-CONTEXT.md`, `03-DISCUSSION-LOG.md`,
`REQUIREMENTS.md`, `PROJECT.md`, `ROADMAP.md`, `SCOPE.md`, `STATE.md`,
`01-PATTERNS.md`, `01-01-SUMMARY.md`, `01-VERIFICATION.md`, `01-VALIDATION.md`,
`01-SECURITY.md`, `02-PATTERNS.md`, `02-01-SUMMARY.md`, `02-VERIFICATION.md`,
`02-VALIDATION.md`, `02-SECURITY.md`, `codebase/ARCHITECTURE.md`,
`codebase/CONCERNS.md`, and `agents/AGENTS.md`.

**Tracked-source gate:** All analog paths were verified with `git -C agents
ls-files --` in the `agents` repository. `.planning` is a symlink to
`agents/planning/`; no gitignored install, runtime mirror, or
`.gsd/capabilities/` path was used. `documents/` and `forge/` paths were not
used as analogs for this planning-only phase.

**Pattern extraction date:** 2026-11-04
