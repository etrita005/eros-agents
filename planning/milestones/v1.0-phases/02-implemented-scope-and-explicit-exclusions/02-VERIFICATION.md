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

## Goal Achievement

### Observable Truths

| # | Truth | Status | Evidence |
|---|-------|--------|----------|
| 1 | The baseline states which capabilities Forge implements today and identifies the authoritative Forge evidence behind each statement. | ✓ VERIFIED | `SCOPE.md:58-71` contains `## Implemented Forge Capabilities` with an eight-row table; every Evidence cell cites repository-relative `forge/` code paths and an authoritative `forge/documents/` document. `gsd-tools query verify.artifacts` reported `all_passed: true` for both declared artifacts. |
| 2 | `SCOPE.md` contains exactly eight Forge capability groups in the D-04 order, each with 2-4 sub-capabilities, applicable FR/NFR identifiers, a code path plus an authoritative document, and an implementation boundary. | ✓ VERIFIED | `SCOPE.md:64-71` has exactly eight five-cell data rows in D-04 order: Controlled build environment and entry; Bazel orchestration and toolchain; Native C++ build path; Foreign CMake integration; Locked Conan + CMake integration; Runtime closure; Debian and symbol packaging; Acceptance and verification tooling. Each sub-capabilities cell has 3-4 comma-separated items; each row cites published FR/NFR IDs (FR-01–FR-20, NFR-01–NFR-04 only). |
| 3 | The Forge table preserves every D-08 limitation: Linux/amd64-only entry and build; incomplete acceptance registry for `TC-ENV-05`–`TC-ENV-07`, `TC-ENTRY-01`–`TC-ENTRY-05`, `TC-CI-03`; ARM64 native execution, registry multi-architecture images, matching target SDK, target execution, remote-cache evidence, and complete CI acceptance not established; `--all --strict` not complete acceptance. | ✓ VERIFIED | Boundary column `SCOPE.md:64-71`: row 1 "Local entry and build flow are Linux/amd64 only" and "ARM64 native execution … not established"; row 2 "registry multi-architecture images and remote-cache evidence are not established"; rows 3-5 "a matching target SDK and target execution are not established"; row 6 target install/execution/robot-system acceptance "not verified"; row 7 target install/uninstall/execution "not verified"; row 8 incomplete registry names all nine TC cases, "Complete CI acceptance and target execution are not established", and "`--all --strict` is not by itself proof of complete release acceptance". Independent `forge/documents/test-design.md:5` confirms the named TC cases are not registered to the runner. |
| 4 | The Forge evidence column describes implementation presence only, and the section states that it is not test success, deployment, or target acceptance evidence. | ✓ VERIFIED | `SCOPE.md:60` states verbatim: "This table records implementation presence, not test success, deployment, or target acceptance. Evidence cites a repository-relative code path and the authoritative Forge document that defines or describes the mechanism; a design document, recipe, example, or unexecuted check is not a passing result." Boundary cells repeat "not established", "not verified", "not claimed", and "not fully registered". |
| 5 | The baseline states which EROS system areas have no implementation in this workspace, without presenting them as delivered. | ✓ VERIFIED | `SCOPE.md:75` frames the table as excluded scope, and `SCOPE.md:79-87` states the absence directly: "No runtime source exists in this workspace"; "no framework… implementation exists"; "no BSP source or hardware enablement"; "no firmware source"; "no pipeline configuration, orchestration service, or automated release infrastructure"; "No deployment, rollback, or target-release automation exists"; "No production or development target platform has been chosen"; "no target execution evidence exists". Workspace inspection confirms only `agents/`, `documents/`, and `forge/` exist; `documents/` contains only LICENSE and README, and no runtime/BSP/firmware/CI source is present. |
| 6 | `SCOPE.md` contains exactly nine EROS exclusion rows in the D-09 order with the exact columns Excluded capability, Area, Status, Reason, Exit criterion, using only Not implemented, Not selected, Not verified, Deferred as statuses. | ✓ VERIFIED | `SCOPE.md:77` exact header; `SCOPE.md:79-87` exactly nine data rows in D-09 order with the exact capability names and Area sequence (Robot runtime and system services, Robot runtime and system services, Linux BSP, Embedded board firmware, CI/CD, CI/CD, Cross-cutting, Cross-cutting, Cross-cutting). Status sequence: Not implemented, Not implemented, Not selected, Not selected, Not implemented, Not implemented, Not selected, Not verified, Deferred. |
| 7 | Every excluded area — runtime, BSP and hardware support, firmware, CI/CD, deployment, and target acceptance — appears in an explicit exclusion list with a specific reason and an actionable exit criterion. | ✓ VERIFIED | `SCOPE.md:79-87` covers all six areas; each Reason cell states why the capability is absent from this workspace, and each Exit criterion states the implementation or evidence that would remove the exclusion (for example, an approved runtime with its own acceptance scope; a selected board with BSP, boot chain, and hardware-enablement evidence; CI/CD that consumes Forge outputs and records execution evidence; target execution evidence for a matching SDK/runtime). |
| 8 | No excluded capability is described as delivered, selected, verified, or accepted in the baseline documents. | ✓ VERIFIED | All nine rows use negative scope statuses; current-state wording is "No … exists", "not selected", "not verified", and "deferred". A grep across `SCOPE.md`, `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` found no positive claim that an excluded capability was delivered, selected, verified, or accepted. The only `PASS` mention is `PROJECT.md:28`, a Phase 1 statement that Forge tooling reports distinct PASS/FAIL/SKIP/BLOCKED result states, not a test-pass claim. |
| 9 | A milestone-level note states that the exclusions are not permanent non-goals and do not redefine the Phase 1 composition. | ✓ VERIFIED | `SCOPE.md:89` states verbatim: "These are milestone-level exclusions, not permanent non-goals. They do not redefine the long-term composition in the sections above." |
| 10 | All Phase 1 sections, headings, and meaning are unchanged; only the two new sections are inserted between Portability Contract and Documentation Boundary, plus the Last updated metadata. | ✓ VERIFIED | `git -C agents diff 64c8f71..HEAD -- planning/SCOPE.md` shows only the two removed `Last updated: 2026-11-03` lines, two replacement `Last updated: 2026-11-04` lines, and the two inserted sections. The nine level-2 headings are in order: Purpose, System Composition, Area Responsibilities, Adjacent-Area Boundaries, Portability Contract, Implemented Forge Capabilities, Explicit Exclusions, Documentation Boundary, Deferred Responsibilities. `Defined`/`Scope baseline defined` remain 2026-11-03. |
| 11 | No file under `documents/` or `forge/` is changed, no capability or exclusion table is duplicated in `PROJECT.md`, `REQUIREMENTS.md`, or `ROADMAP.md`, and no `SKELETON.md` or `COVERAGE.md` is created. | ✓ VERIFIED | `git -C documents status --porcelain` and `git -C forge status --porcelain` are both empty; `git -C agents diff --check` exits 0. Each new table header occurs exactly once across the four planning documents. `SKELETON.md` and `COVERAGE.md` are absent from the phase directory. Phase content commits `f06e79b` and `ce64fe8` touch only `planning/SCOPE.md`; `d3bc768` is completion bookkeeping. |

**Score:** 11/11 truths verified (0 present, behavior-unverified)

### Required Artifacts

| Artifact | Expected | Status | Details |
|----------|----------|--------|---------|
| `agents/planning/SCOPE.md` | Authoritative scope baseline extended with an evidence-backed Forge capability inventory and a milestone-level explicit exclusion table | ✓ VERIFIED | 105-line baseline; contains `## Implemented Forge Capabilities` (`SCOPE.md:58`) and `## Explicit Exclusions` (`SCOPE.md:73`); both tables substantive and grounded in existing, tracked Forge paths and documents. `gsd-tools query verify.artifacts` returned `all_passed: true`, 2/2, exit 0. |
| `agents/planning/SCOPE.md` | Nine-row EROS exclusion table with status, reason, and exit criterion | ✓ VERIFIED | `SCOPE.md:77-87`; exactly nine rows, five columns, four allowed statuses, non-empty specific reasons and exit criteria. |

### Key Link Verification

| From | To | Via | Status | Details |
|------|----|-----|--------|---------|
| `agents/planning/SCOPE.md` | `forge/` source paths | `## Implemented Forge Capabilities` Evidence cells cite repository-relative Forge code paths | WIRED | Pattern `` `forge/ `` found; all 25 unique cited paths exist and are tracked in the Forge repository (`git -C forge ls-files --error-unmatch`). `verify.key-links` link 1 verified. |
| `agents/planning/SCOPE.md` | `forge/documents/` authoritative documents | Evidence cells cite authoritative Forge documents with stable section references | WIRED | `forge/documents/requirements.md` §3.1, `technical-design.md` §2-§3 and §6, `cmake-forge.md` §Install And Export, `cmake-conan-forge.md` §Action And Final Outputs, `deb-packaging.md` §Debug And Test Archives, `test-design.md` §1, and `forge/tests/README.md` §Results And Evidence all exist. `verify.key-links` link 2 verified. |
| `agents/planning/SCOPE.md` | `forge/documents/requirements.md` FR and NFR identifiers | The Forge requirement refs column cites only FR-01–FR-20 and NFR-01–NFR-04 | WIRED | Allow-list scan found no identifier outside the published set. `verify.key-links` link 3 verified. |
| `agents/planning/SCOPE.md` | `agents/planning/SCOPE.md` Phase 1 composition areas | Each exclusion Area cell reuses a Phase 1 area name or Cross-cutting | WIRED | Area values are exactly Robot runtime and system services, Linux BSP, Embedded board firmware, CI/CD, and Cross-cutting. `verify.key-links` link 4 verified. |
| `agents/planning/REQUIREMENTS.md` | `agents/planning/SCOPE.md` | Requirements SCOPE-02 and SCOPE-03 are answered by the two new sections | WIRED | `REQUIREMENTS.md:14-15` defines SCOPE-02/SCOPE-03; `REQUIREMENTS.md:9` links to `.planning/SCOPE.md`; the new sections answer both. `verify.key-links` link 5 verified. |

**Key links:** 5/5 WIRED (`all_verified: true`, exit 0).

### Data-Flow Trace (Level 4)

N/A — documentation-only phase. The artifacts are Markdown documents with no dynamic data source, query, or rendered values. The equivalent check is evidence grounding: all 25 cited Forge paths exist and are tracked, and all cited document sections exist.

### Behavioral Spot-Checks

| Behavior | Command | Result | Status |
|----------|---------|--------|--------|
| Plan Task 3 validation suite | 27 `<automated>` checks extracted from `02-01-PLAN.md` Task 3 and run from the workspace root | 27/27 exited 0, including heading order, row/cell counts, D-04/D-09 order, identifier allow-list, cited-path tracking, D-08 boundary facts, normalized pre-edit snapshot diff, duplicate-header counts, repository status, and trailing-whitespace checks | ✓ PASS |
| Artifact verification | `gsd_run query verify.artifacts <plan>` | `all_passed: true`, 2/2, exit 0 | ✓ PASS |
| Key-link verification | `gsd_run query verify.key-links <plan>` | `all_verified: true`, 5/5, exit 0 | ✓ PASS |
| Decision coverage | `gsd_run query check.decision-coverage-verify <phase> <context>` | 15/15 honored, `not_honored: []`, exit 0 | ✓ PASS |
| Commit verification | `gsd_run query verify.commits f06e79b ce64fe8 d3bc768` from the `agents` repository root | `all_valid: true`, 3/3, exit 0 | ✓ PASS |
| Runnable application behavior | — | No runnable entry points; documentation-only phase | ? SKIP (N/A) |

### Probe Execution

N/A — no probes are declared in the PLAN or SUMMARY, and this is not a migration, CLI, or tooling phase. No `scripts/*/tests/probe-*.sh` files exist for this phase.

### Decision Coverage

All 15 trackable `02-CONTEXT.md` decisions are honored by shipped artifacts: 15/15 honored, 0 not honored. D-01 through D-15 are reflected in the two new `SCOPE.md` sections, the insertion point, the evidence discipline, and the exclusion status vocabulary.

### Requirements Coverage

| Requirement | Source Plan | Description | Status | Evidence |
|-------------|-------------|-------------|--------|----------|
| SCOPE-02 | `02-01-PLAN.md` | Define Forge's implemented capabilities and boundaries separately from unimplemented EROS system scope. | ✓ SATISFIED | `SCOPE.md:58-71` eight-row Forge inventory with FR/NFR references, code-plus-document evidence, and D-08 boundaries; `SCOPE.md:60` evidence-vs-acceptance guard. `REQUIREMENTS.md:64` still reads `Pending`; per the phase-completion contract this status update is performed after verification and is not a Phase 2 gap. |
| SCOPE-03 | `02-01-PLAN.md` | Record explicit exclusions for this baseline so future runtime, hardware, firmware, and CI/CD work is not presented as delivered. | ✓ SATISFIED | `SCOPE.md:73-89` nine-row exclusion table covering runtime, application framework, Linux BSP, Zephyr firmware, CI/CD, deployment/rollback, target hardware, target acceptance, and OTA; specific reasons, actionable exit criteria, and the milestone-level note. `REQUIREMENTS.md:65` still reads `Pending`; updating it is likewise a post-verification tracking step. |

**Orphaned requirements:** None. Phase 2's requirement IDs are exactly SCOPE-02 and SCOPE-03; both are claimed by `02-01-PLAN.md` and accounted for. No other requirement is mapped to Phase 2 in `REQUIREMENTS.md` or `ROADMAP.md`.

### Anti-Patterns Found

| File | Line | Pattern | Severity | Impact |
|------|------|---------|----------|--------|
| `agents/planning/ROADMAP.md` | 57, 68 | `TBD` placeholders for Phase 3 plans and progress | ℹ️ Info | Pre-existing future-phase placeholders, not introduced by this phase and not Phase 2 deliverable debt: Phase 3 plans have not been created. No `TBD`, `FIXME`, `XXX`, `TODO`, `HACK`, or `PLACEHOLDER` marker exists in `SCOPE.md`. |
| `agents/planning/milestone.lock` | 1 | Untracked GSD runtime lock file in the planning root | ℹ️ Info | Runtime state created during execution (mtime 2026-11-04 02:22), not phase content and not claimed by the summary. It does not affect `documents/` or `forge/` cleanliness, and `git -C agents diff --check` is clean. |

### Human Verification Required

N/A — documentation-only phase with no user-facing UI, runtime behavior, external service, or real-time element. The two `02-VALIDATION.md` manual-only items (semantic accuracy of the Forge inventory, and scope/status/reason validity of the nine exclusions) were evaluated directly against `02-CONTEXT.md` D-04 through D-12, the cited Forge code and documents, and workspace contents; the evidence is recorded in Observable Truths rows 1–9 and the Requirements Coverage table. No unresolved human item remains, and no truth is behavior-dependent.

### Summary Claim Verification

| Claim | Evidence | Result |
|-------|----------|--------|
| Task 1 commit `f06e79b`; Task 2 commit `ce64fe8`; plan metadata commit `d3bc768` | All three hashes resolve as commits in the `agents` repository and are ancestors of HEAD (`5e325ec`). `f06e79b` and `ce64fe8` touch only `planning/SCOPE.md`; `d3bc768` records `02-01-SUMMARY.md`, `ROADMAP.md`, `STATE.md`, and `state.json`. | MATCH |
| `actuals.commits: 2` and plan head `64c8f71..ce64fe8` | `git rev-list --count 64c8f71..ce64fe8` = 2; `plan_head_before` and `plan_head_after` match the stated hashes. | MATCH |
| Only `agents/planning/SCOPE.md` was content-modified | `git show --stat` for the two content commits; the remaining changed files are completion metadata, as the summary states. | MATCH |
| 27 labeled validation checks all passed | Full Task 3 suite re-run: 27/27 exit 0, including the normalized pre-edit snapshot diff against `/tmp/eros-scope-before.md`. | MATCH |
| Phase 1 sections unchanged | `git diff 64c8f71..HEAD -- planning/SCOPE.md` contains only the two inserted sections and the two `Last updated` line changes. | MATCH |
| All 25 cited `forge/` paths exist and are tracked | Each unique path passed `test -e` and `git -C forge ls-files --error-unmatch`. | MATCH |
| `documents/` and `forge/` clean | Both `status --porcelain` outputs empty after successful git invocations. | MATCH |
| No `SKELETON.md` or `COVERAGE.md` | Both paths absent from the phase directory. | MATCH |
| No deviations from plan | The two new sections match the plan's mandated rows, columns, group order, and verbatim guard/note/framing sentences; both `Last updated` lines are 2026-11-04. | MATCH |

### Gaps Summary

None. The phase goal is achieved in the codebase, not merely in the summary: `agents/planning/SCOPE.md` now contains an authoritative eight-group, evidence-backed inventory of Forge's implemented capabilities with explicit D-08 boundaries, plus a nine-row milestone-level EROS exclusion table with specific reasons and actionable exit criteria. Phase 1 sections are unchanged, `PROJECT.md`/`REQUIREMENTS.md`/`ROADMAP.md` continue to link to `.planning/SCOPE.md` without duplicating the new tables, and `documents/` and `forge/` are untouched. All 27 plan validation checks, 2/2 artifact checks, 5/5 key links, and 15/15 decision-coverage checks pass.

One expected cross-phase lifecycle observation, not a Phase 2 gap: `verification.status` for Phase 1 now reports `stale` because Phase 2 intentionally changed `SCOPE.md`, which is one of Phase 1's covered files. Phase 1 remains historically verified; its report is not re-opened by this phase.

---

_Verified: 2026-11-04T01:16:25Z_
_Verifier: the agent (gsd-verifier)_
