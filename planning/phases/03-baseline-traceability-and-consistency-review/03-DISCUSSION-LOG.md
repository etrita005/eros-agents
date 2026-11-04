# Phase 3: Baseline Traceability and Consistency Review - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-11-04
**Phase:** 3-Baseline Traceability and Consistency Review
**Areas discussed:** Traceability chain granularity, Evidence status vocabulary,
Review scope and authority, Review artifact and disposition

---

## Traceability Chain Granularity

### Chain depth

| Option | Description | Selected |
|--------|-------------|----------|
| Full chain | Requirement → phase → plan → summary → verification → status. | ✓ |
| Phase map only | Requirement → phase → status with a short evidence note. | |
| Plan level only | Requirement → phase → delivering plan. | |

**User's choice:** Full chain. The table must make the evidence path explicit,
not just the phase assignment.

### v2 requirements

| Option | Description | Selected |
|--------|-------------|----------|
| v1 only + note | Six v1 rows; v2 deferred and excluded from v1 coverage. | ✓ |
| Separate v2 table | A second table with Deferred rows. | |
| v1+v2 same table | One table with extra status rules. | |

**User's choice:** v1 only plus a note. v2 must not inflate or distort v1
coverage.

### Current phase status

| Option | Description | Selected |
|--------|-------------|----------|
| In progress → Verified | TRACE-01/REVIEW-01 start In progress; phase verification changes them to Verified. | ✓ |
| Documented only | Mark them Documented and leave verification outside the table. | |
| Verified immediately | Mark Verified as soon as the review is written. | |

**User's choice:** In progress until Phase 3 verification passes.

### Table location

| Option | Description | Selected |
|--------|-------------|----------|
| REQUIREMENTS.md | Extend the existing Traceability table with Plan, Summary, Verification, Evidence type, and Status. | ✓ |
| SCOPE.md | Add a new Requirement Traceability section. | |
| Separate TRACEABILITY.md | Maintain a standalone chain file. | |

**User's choice:** Extend `REQUIREMENTS.md`.

---

## Evidence Status Vocabulary

### Status model

| Option | Description | Selected |
|--------|-------------|----------|
| Status + Evidence type | Separate completion status from the kind of evidence. | ✓ |
| Three-level status | Documented / Implemented / Verified as one status. | |
| Two status columns | Delivery status plus acceptance status. | |

**User's choice:** Separate `Status` and `Evidence type` columns.

### Status values

| Option | Description | Selected |
|--------|-------------|----------|
| In progress / Verified | Only two values; evidence type carries the rest. | ✓ |
| Four values | In progress / Documented / Implemented / Verified. | |
| Complete / Verified | Complete for written docs, Verified after phase verification. | |

**User's choice:** `In progress` and `Verified` only.

### Verified meaning

| Option | Description | Selected |
|--------|-------------|----------|
| Phase verification passed | The requirement's deliverable passed its phase VERIFICATION.md. | ✓ |
| Implementation + local test | Code exists and local tests passed. | |
| Manual review only | A person reviewed the document. | |

**User's choice:** Verified means the corresponding phase VERIFICATION passed; it
does not mean Forge full acceptance, target ABI, deployment, or robot-system
acceptance.

### Evidence type values

| Option | Description | Selected |
|--------|-------------|----------|
| Four types | Documented scope; Forge implementation inventory; Documented exclusions; Review and traceability. | ✓ |
| Three types | Documented scope; Forge implementation inventory; Review artifact. | |
| Free text | No fixed vocabulary. | |

**User's choice:** Four fixed types, mapped per requirement in CONTEXT.md.

---

## Review Scope and Authority

### Documents in scope

| Option | Description | Selected |
|--------|-------------|----------|
| Four baseline documents | PROJECT.md, REQUIREMENTS.md, ROADMAP.md, SCOPE.md. | ✓ |
| Four + STATE | Also review operational state. | |
| Expanded | Also review codebase map and Forge documents. | |

**User's choice:** Four baseline documents. `STATE.md` is operational state and
is not part of the baseline audit.

### Conflict authority

| Option | Description | Selected |
|--------|-------------|----------|
| Domain authority | SCOPE for scope; REQUIREMENTS for IDs/status; ROADMAP for phase assignment; PROJECT for identity/constraints. | ✓ |
| PROJECT wins | All conflicts defer to PROJECT.md. | |
| No hierarchy | Record all conflicts without a resolution rule. | |

**User's choice:** Domain authority. Cross-domain conflicts are recorded as
unresolved with a disposition.

### Review method

| Option | Description | Selected |
|--------|-------------|----------|
| Automated + manual | Structural scripts plus semantic reading. | ✓ |
| Automated only | Script checks only. | |
| Manual only | Human reading only. | |

**User's choice:** Automated structural checks plus manual semantic review.

---

## Review Artifact and Disposition

### Review location

| Option | Description | Selected |
|--------|-------------|----------|
| Independent 03-REVIEW.md | Phase artifact; SCOPE.md remains the authoritative baseline. | ✓ |
| SCOPE.md section | Embed review history in the baseline. | |
| PROJECT.md section | Embed review in the project document. | |

**User's choice:** `03-REVIEW.md` as a separate phase artifact.

### Fix policy

| Option | Description | Selected |
|--------|-------------|----------|
| Mechanical fix + record | Fix IDs, phase assignments, statuses, dates, links, and counts; record semantic conflicts. | ✓ |
| Record only | Do not modify baseline documents. | |
| Fix everything | Rewrite any inconsistency, including scope meaning. | |

**User's choice:** Mechanical fixes only; semantic/scope conflicts are recorded,
not silently rewritten.

### Disposition values

| Option | Description | Selected |
|--------|-------------|----------|
| Fixed / Deferred / Accepted / Blocked | Every finding gets an actionable disposition. | ✓ |
| Resolved / Open / Deferred | Simpler status vocabulary. | |
| Free text | No fixed disposition values. | |

**User's choice:** `Fixed`, `Deferred`, `Accepted`, `Blocked`.

### Severity levels

| Option | Description | Selected |
|--------|-------------|----------|
| High / Medium / Low | High = requirement/phase misread; Medium = cross-doc inconsistency; Low = wording/date/count. | ✓ |
| Blocker / Warning / Info | Common review-tool vocabulary. | |
| No severity | Only findings and dispositions. | |

**User's choice:** `High`, `Medium`, `Low`.

---

## the agent's Discretion

- Wording of the review summary and finding descriptions.
- Whether to add a short methodology paragraph.
- Markdown table wrapping and formatting.
- Ordering of findings within a severity group.

## Deferred Ideas

- Reviewing `STATE.md` as part of the baseline audit.
- Expanding the review to the codebase map and Forge authoritative documents.
- Re-running Phase 1/2 verifiers after mechanical fixes.
- Changing scope meaning or requirements during mechanical fixes.
- Creating a separate TRACEABILITY.md or embedding the review in SCOPE.md.
