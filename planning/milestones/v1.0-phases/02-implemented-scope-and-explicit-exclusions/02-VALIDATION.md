---
phase: "2"
slug: "implemented-scope-and-explicit-exclusions"
status: validated
nyquist_compliant: false
wave_0_complete: true
created: "2026-11-04"
---

# Phase 2 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | none — documentation-only phase; structural assertions use shell grep/awk/test |
| **Config file** | none |
| **Quick run command** | `grep -Fqx '## Implemented Forge Capabilities' agents/planning/SCOPE.md && grep -Fqx '## Explicit Exclusions' agents/planning/SCOPE.md` |
| **Full suite command** | The 27-command Task 3 validation suite in `02-01-PLAN.md`, plus `git -C agents diff --check` |
| **Estimated runtime** | ~1 second |

---

## Sampling Rate

- **After every task commit:** Run the Task 3 structure/evidence checks
- **After every plan wave:** Run `git -C agents diff --check`
- **Before `/gsd-verify-work`:** Structural checks must pass; capability and exclusion semantics remain manual
- **Max feedback latency:** 1 second

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|------------|-----------------|-----------|-------------------|-------------|--------|
| 2-01-01 | 01 | 1 | SCOPE-02 | T-02-02 / T-02-06 | Forge evidence cannot be read as test or target acceptance | structural | `grep -Fqx '## Implemented Forge Capabilities' agents/planning/SCOPE.md` | ✅ | ✅ green |
| 2-01-02 | 01 | 1 | SCOPE-03 | T-02-03 / T-02-04 | Exclusions have explicit scope status, reason, and exit criterion | structural | `grep -Fqx '## Explicit Exclusions' agents/planning/SCOPE.md` | ✅ | ✅ green |
| 2-01-03 | 01 | 1 | SCOPE-02, SCOPE-03 | T-02-01 / T-02-05 | Phase 1 content and repository boundaries are preserved | structural | `git -C agents diff --check && test -z "$(git -C documents status --porcelain)"` | ✅ | ✅ green |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

Existing infrastructure covers all phase requirements. No test framework, stubs, or fixtures are needed for this documentation-only phase.

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Eight-group Forge inventory accurately reflects implementation presence and boundaries | SCOPE-02 | The choice of groups, the wording of sub-capabilities, and whether a boundary is complete are semantic judgments; literal checks cannot establish accuracy. | Read `## Implemented Forge Capabilities`; confirm the eight D-04 groups, 2-4 sub-capabilities each, FR/NFR refs, code-plus-document evidence, and all D-08 boundaries; compare with `02-CONTEXT.md` D-04 through D-08 and Forge `requirements.md`/`test-design.md`. |
| Nine exclusion rows are correctly scoped, statused, reasoned, and actionable | SCOPE-03 | Whether a reason is accurate and an exit criterion is sufficient is a judgment about project intent, not a string match. | Read `## Explicit Exclusions`; confirm the nine D-09 rows, exact area/status sequences, specific reasons, and exit criteria; confirm the milestone-level note; compare with `02-CONTEXT.md` D-09 through D-12. |

*No automated test framework exists; semantic document review is the acceptance mechanism.*

---

## Validation Sign-Off

- [x] All tasks have `<automated>` verify or Wave 0 dependencies
- [x] Sampling continuity: no 3 consecutive tasks without automated verify
- [x] Wave 0 covers all MISSING references
- [x] No watch-mode flags
- [x] Feedback latency < 1s
- [ ] `nyquist_compliant: true` set in frontmatter — intentionally false: semantic document review is manual-only

**Approval:** validated with manual-only items 2026-11-04
