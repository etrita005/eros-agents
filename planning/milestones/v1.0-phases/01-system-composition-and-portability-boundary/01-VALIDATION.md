---
phase: "1"
slug: "system-composition-and-portability-boundary"
status: validated
nyquist_compliant: false
wave_0_complete: true
created: "2026-11-03"
---

# Phase 1 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | none — documentation-only phase; structural assertions use shell grep/test |
| **Config file** | none |
| **Quick run command** | `grep -Fqx '# EROS Scope Baseline' agents/planning/SCOPE.md` |
| **Full suite command** | `git -C agents diff --check` plus the Task 3 structure/link suite in `01-01-PLAN.md` |
| **Estimated runtime** | ~1 second |

---

## Sampling Rate

- **After every task commit:** Run the Task 3 structure/link checks
- **After every plan wave:** Run `git -C agents diff --check`
- **Before `/gsd-verify-work`:** Structural checks must pass; semantic composition review remains manual
- **Max feedback latency:** 1 second

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|------------|-----------------|-----------|-------------------|-------------|--------|
| 1-01-01 | 01 | 1 | SCOPE-01 | T-01-01 / T-01-02 | Scope text cannot be mistaken for implementation or acceptance evidence | structural | `grep -Fqx '# EROS Scope Baseline' agents/planning/SCOPE.md` | ✅ | ✅ green |
| 1-01-02 | 01 | 1 | SCOPE-04 | T-01-03 / T-01-05 | Portability text records limits and examples, not support commitments | structural | `grep -Fq 'No additional cross-version ABI compatibility is promised.' agents/planning/SCOPE.md` | ✅ | ✅ green |
| 1-01-03 | 01 | 1 | SCOPE-01, SCOPE-04 | T-01-04 | All planning documents link to the single baseline without duplication | structural | `grep -Fq '**Scope baseline:** `.planning/SCOPE.md`' agents/planning/ROADMAP.md` | ✅ | ✅ green |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

Existing infrastructure covers all phase requirements. No test framework, stubs, or fixtures are needed for this documentation-only phase.

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Two-layer, five-area composition with one responsibility and explicit boundary per area | SCOPE-01 | Composition correctness is semantic: literal checks cannot determine whether a responsibility statement is accurate, non-duplicated, or genuinely bounded. | Read `agents/planning/SCOPE.md` sections `System Composition`, `Area Responsibilities`, and `Adjacent-Area Boundaries`; confirm exactly five areas in two named layers, each with one responsibility and a boundary that distinguishes it from adjacent areas; compare against D-04 through D-09 in `01-CONTEXT.md`. |
| Portability contract wording and scope | SCOPE-04 | Whether the contract is meaningful and correctly limited is a judgment about meaning, not a string match. | Read `Portability Contract`; confirm common source/public APIs for runtime and application framework, platform-specific BSP/firmware, no cross-platform binary guarantee, no extra ABI promise, and Linux/amd64/ARM64 as examples only; compare against D-11 through D-13. |

*No automated test framework exists; semantic document review is the acceptance mechanism.*

---

## Validation Sign-Off

- [x] All tasks have `<automated>` verify or Wave 0 dependencies
- [x] Sampling continuity: no 3 consecutive tasks without automated verify
- [x] Wave 0 covers all MISSING references
- [x] No watch-mode flags
- [x] Feedback latency < 1s
- [ ] `nyquist_compliant: true` set in frontmatter — intentionally false: semantic document review is manual-only

**Approval:** validated with manual-only items 2026-11-03
