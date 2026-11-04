# Project Retrospective

*A living document updated after each milestone. Lessons feed forward into future planning.*

## Milestone: v1.0 — Scope Baseline

**Shipped:** 2026-11-05
**Phases:** 3 | **Plans:** 3 | **Tasks:** 9

### What Was Built

- Two-layer, five-area EROS composition with per-area responsibilities, adjacent boundaries, and a portability contract in `.planning/SCOPE.md`.
- Eight-group Forge capability inventory with FR/NFR references, code-plus-document evidence, and explicit implementation boundaries.
- Nine-row EROS exclusion table with status, reason, and exit criterion, plus a seven-column traceability chain and four-document consistency review.

### What Worked

- Phase gates kept scope, requirements, roadmap, traceability, and verification synchronized.
- Domain authority resolved cross-document conflicts without letting one document silently override another.
- Separating implementation presence, local verification, and target acceptance prevented overclaiming Forge maturity.
- Refresh-after-completion verification repaired stale fingerprints before milestone close.

### What Was Inefficient

- Completion tracking changed covered files after verification, repeatedly making prior fingerprints stale; refresh should be built into phase completion.
- The configured Sonnet model required paid authentication, so planning and verification used the current session model.
- The phase 3 plan was initially truncated and needed manual completion plus targeted checker revisions.
- The milestone audit surfaced an OTA deferral propagation gap only after phase 3 had closed.

### Patterns Established

- One authoritative `SCOPE.md`; project documents link rather than copy it.
- Fixed capability and exclusion table shapes with explicit status vocabularies.
- Full traceability chain: requirement → phase → plan → summary → verification → evidence type → status.
- Independent `03-REVIEW.md` with domain authority, severity, disposition, and exit-oriented findings.
- Documentation-only Nyquist coverage is recorded as manual-only, not passed.

### Key Lessons

1. Treat completion bookkeeping as part of the verification lifecycle; re-verify after status/date changes.
2. Propagate scope exclusions into the requirements registry when they must survive milestone archival.
3. Keep review artifacts separate from authoritative baselines so review history does not rewrite scope.
4. Record manual-only validation explicitly rather than generating unrelated tests for documentation.

### Cost Observations

- Model mix: not instrumented for this milestone; the configured paid model was unavailable, so the current session model was used.
- Sessions: multiple interactive and delegated sessions across three phases.
- Notable: documentation-only phases still benefit from independent verification, but exhaustive literal checks created substantial plan-maintenance overhead.

---

## Cross-Milestone Trends

### Process Evolution

| Milestone | Sessions | Phases | Key Change |
|-----------|----------|--------|------------|
| v1.0 | multiple | 3 | Established the GSD planning baseline and evidence-boundary workflow |

### Cumulative Quality

| Milestone | Tests | Coverage | Zero-Dep Additions |
|-----------|-------|----------|-------------------|
| v1.0 | N/A (documentation-only) | Manual semantic review | 0 |

### Top Lessons (Verified Across Milestones)

1. Keep authoritative documents singular and link to them.
2. Separate design, implementation, test, and acceptance evidence.
