# Phase 2: Implemented Scope and Explicit Exclusions - Context

**Gathered:** 2026-11-03
**Status:** Ready for planning

<domain>
## Phase Boundary

Add an evidence-backed inventory of Forge's implemented capabilities and an
explicit, reasoned exclusion list for the EROS system areas that are not
implemented in this workspace. This phase produces planning documentation only.
It does not implement runtime, BSP, firmware, Forge, CI/CD, deployment, or
hardware support, and it does not run acceptance tests or claim target
acceptance.

</domain>

<decisions>
## Implementation Decisions

### Document Structure

- **D-01:** Extend the existing authoritative `agents/planning/SCOPE.md`; do not
  create a second scope document. Insert two new sections after
  `## Portability Contract` and before `## Documentation Boundary`:
  `## Implemented Forge Capabilities` and `## Explicit Exclusions`. Preserve the
  existing Phase 1 sections, headings, and meaning. — **Reversibility:** reversible.
- **D-02:** Update the document's `**Last updated:**` line. Do not modify
  `documents/`; the Phase 1 documentation-boundary rule remains in force.
- **D-03:** `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` already link to
  `.planning/SCOPE.md`; do not duplicate the new tables in those files. Only
  status/traceability updates required by phase completion may touch them.

### Forge Capability Inventory

- **D-04:** Use exactly eight capability groups, in this order:
  1. Controlled build environment and entry
  2. Bazel orchestration and toolchain
  3. Native C++ build path
  4. Foreign CMake integration
  5. Locked Conan + CMake integration
  6. Runtime closure
  7. Debian and symbol packaging
  8. Acceptance and verification tooling
- **D-05:** The Forge table has columns:
  `Capability group | Implemented sub-capabilities | Forge requirement refs | Evidence | Implementation boundary / not covered`.
  Use one row per capability group. Each `Implemented sub-capabilities` cell
  contains 2–4 short keywords, not a second table. Each row cites FR/NFR IDs
  from `forge/documents/requirements.md` where applicable.
- **D-06:** Include only capability groups with code evidence in the Forge table.
  Partial or unaccepted scope stays in the same row's
  `Implementation boundary / not covered` column. Do not move a partial Forge
  capability into the exclusion table.
- **D-07:** Evidence must combine a repository-relative code path and an
  authoritative Forge document. Prefer stable file/section references; line
  numbers are optional. Evidence describes implementation presence, not test or
  acceptance success.
- **D-08:** The Forge table must explicitly preserve these boundaries:
  - the implemented entry and build flow is local Linux/amd64 only;
  - acceptance registry coverage is incomplete: `TC-ENV-05`–`TC-ENV-07`,
    `TC-ENTRY-01`–`TC-ENTRY-05`, and `TC-CI-03` are not fully registered;
  - ARM64 native execution, registry multi-architecture images, a matching
    target SDK, target execution, remote-cache evidence, and complete CI
    acceptance are not established;
  - `--all --strict` is not by itself proof of complete release acceptance.

### Explicit Exclusions

- **D-09:** Use a separate EROS exclusion table with columns:
  `Excluded capability | Area | Status | Reason | Exit criterion`.
  Use exactly these nine rows, in this order:
  1. Robot runtime and system services implementation — Area: Robot runtime and
     system services — Status: Not implemented.
  2. Application framework, component model, lifecycle, and package model —
     Area: Robot runtime and system services — Status: Not implemented.
  3. Linux BSP and hardware support — Area: Linux BSP — Status: Not selected.
  4. Zephyr-based embedded board firmware — Area: Embedded board firmware —
     Status: Not selected.
  5. CI/CD pipeline and orchestration implementation — Area: CI/CD — Status: Not
     implemented.
  6. Deployment and rollback automation — Area: CI/CD — Status: Not
     implemented.
  7. Target hardware selection — Area: Cross-cutting — Status: Not selected.
  8. Target ABI, deployment, and robot-system acceptance — Area: Cross-cutting —
     Status: Not verified.
  9. System update / OTA design and implementation — Area: Cross-cutting —
     Status: Deferred.
- **D-10:** Use exactly these status values in the exclusion table:
  `Not implemented`, `Not selected`, `Not verified`, `Deferred`. Do not use
  `partial` as an exclusion status; partial Forge scope belongs in the Forge
  table's boundary column.
- **D-11:** Each `Reason` cell states why the item is absent from this
  workspace. Each `Exit criterion` cell states what implementation or evidence
  would remove the exclusion (for example, an approved runtime implementation,
  a selected target board with BSP and firmware, or target execution evidence).
- **D-12:** State explicitly that these are milestone-level exclusions, not
  permanent non-goals. They do not redefine the long-term composition in the
  Phase 1 sections.

### Evidence and Claims

- **D-13:** Use the codebase map (`.planning/codebase/`, analysis date
  2026-11-02), Forge authoritative documents, and repository-relative source
  paths as evidence. Do not run Forge acceptance, Docker, or target tests in
  this documentation phase. Do not present a design document, recipe, example,
  or unexecuted check as a passing result.
- **D-14:** Distinguish implementation presence from local test evidence and
  target acceptance. The Forge table's evidence column may cite tests only as
  existing test code or documented test design; it must not state that a suite
  passed unless a result artifact is cited.
- **D-15:** Do not describe Forge as the whole EROS operating system. Keep the
  Phase 1 two-layer composition and Forge/CI-CD boundary intact.

### the agent's Discretion

- Exact wording of capability-group descriptions and exclusion reasons.
- Which stable file/section references to prefer for each evidence cell.
- Whether to add a short introductory paragraph before each new table.
- Table width, line wrapping, and use of Markdown footnotes, provided all
  required columns, rows, and status values remain explicit.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Baseline and Requirements

- `.planning/SCOPE.md` — authoritative composition baseline; Phase 2 extends it.
- `.planning/PROJECT.md` — project identity, validated capabilities, active
  requirements, constraints, and evidence boundaries.
- `.planning/REQUIREMENTS.md` — `SCOPE-02` and `SCOPE-03` define this phase.
- `.planning/ROADMAP.md` — Phase 2 goal, success criteria, and dependency on
  Phase 1.
- `.planning/phases/01-system-composition-and-portability-boundary/01-CONTEXT.md`
  — locked Phase 1 decisions, especially D-01 through D-14.

### Codebase Evidence

- `.planning/codebase/ARCHITECTURE.md` — implemented Forge components, data flow,
  build paths, runtime closure, and packaging.
- `.planning/codebase/STACK.md` — technologies, toolchain, and runtime
  dependencies.
- `.planning/codebase/STRUCTURE.md` — repository and Forge module layout.
- `.planning/codebase/TESTING.md` — test runner, acceptance structure, and
  evidence limitations.
- `.planning/codebase/CONCERNS.md` — acceptance-registry gaps, packaging
  concerns, and security/reproducibility boundaries.

### Forge Authoritative Documents

- `forge/AGENTS.md` — Forge scope, controlled-environment rules, and verification
  boundaries.
- `forge/README.md` — supported Linux/amd64 entry, image identity, examples, and
  explicit non-validated configurations.
- `forge/documents/requirements.md` — Forge requirements, decisions, FR/NFR IDs,
  and release-admission boundaries.
- `forge/documents/technical-design.md` — architecture and mechanism contracts.
- `forge/documents/test-design.md` — acceptance cases and evidence boundaries;
  records unregistered cases and non-accepted scopes.
- `forge/tests/README.md` — runnable checks, prerequisites, and PASS/FAIL/SKIP/
  BLOCKED semantics.
- `forge/examples/README.md` — consumer examples, explicitly not acceptance
  evidence.

### Project Instructions

- `agents/AGENTS.md` — multi-repository ownership, evidence boundaries, and
  commit rules. Root `AGENTS.md` links to this file.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets

- `SCOPE.md` already contains the two-layer composition and the Forge/CI-CD
  boundary; Phase 2 adds status and exclusions without redefining them.
- The codebase map provides a compact, dated inventory of Forge architecture,
  structure, tests, and concerns.
- Forge's authoritative requirements and test design provide FR/NFR IDs and
  acceptance boundaries that the capability table can cite.

### Established Patterns

- One authoritative baseline with link-not-copy references from the project
  planning documents.
- Evidence is separated into requirements, design, implementation, test results,
  and target acceptance.
- Planning documents use Markdown tables with explicit headings and
  repository-relative paths.
- Forge docs and the codebase map already distinguish implemented mechanisms
  from unaudited or unaccepted scopes.

### Integration Points

- `SCOPE.md` gains the two new sections; `PROJECT.md`, `REQUIREMENTS.md`, and
  `ROADMAP.md` continue to link rather than duplicate.
- Phase 3 will review consistency and traceability across these documents.
- `documents/` remains untouched until robot-OS functionality exists.

</code_context>

<specifics>
## Specific Ideas

- The Forge inventory is eight rows, not a 20-row FR table and not an
  architecture component dump.
- Every Forge row has an implementation-boundary column so partial coverage is
  visible without moving the row into the exclusion table.
- The EROS exclusion table is nine capability rows, with a status vocabulary of
  `Not implemented`, `Not selected`, `Not verified`, and `Deferred`.
- Exclusions include an `Exit criterion`, making them actionable rather than a
  static list of non-goals.
- The baseline must never imply that Forge code, local tests, cross-compilation,
  or recipes establish target acceptance.

</specifics>

<deferred>
## Deferred Ideas

- Running Forge acceptance, Docker integration, or target tests as part of this
  planning phase.
- Re-mapping the Forge codebase before the phase plan.
- Updating `documents/` with external architecture or core-technology material.
- Implementing any excluded runtime, BSP, firmware, CI/CD, deployment, OTA, or
  Forge expansion scope.
- Adding a full 20-row FR acceptance matrix or per-test status table.

</deferred>

---

*Phase: 2-Implemented Scope and Explicit Exclusions*
*Context gathered: 2026-11-03*
