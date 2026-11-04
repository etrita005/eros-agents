---
phase: "1"
slug: "system-composition-and-portability-boundary"
status: verified
threats_open: 0
asvs_level: 1
created: "2026-11-03"
---

# Phase 1 — Security

> Per-phase security contract: threat register, accepted risks, and audit trail.

---

## Trust Boundaries

| Boundary | Description | Data Crossing |
|----------|-------------|---------------|
| Planning baseline to reader | Normative scope text can be misread as implemented capability, a support commitment, or an acceptance claim. | Scope statements and evidence-status wording |
| Workspace to `documents/` repository | The phase must not write outward-facing documentation or duplicate planning material into the documents repository. | Planning text and repository writes |
| Planning baseline to downstream phases | Duplicated or inconsistent scope text can propagate into Phase 2 exclusions and Phase 3 traceability. | Requirement identifiers, links, and phase assignments |

---

## Threat Register

| Threat ID | Category | Component | Severity | Disposition | Mitigation | Status |
|-----------|----------|-----------|----------|-------------|------------|--------|
| T-01-01 | Tampering | `agents/planning/SCOPE.md` normative content | high | mitigate | Task 1 specified exact area names, responsibilities, boundaries, portability rows, and D-01 through D-14 literals; Task 3 re-checked every required literal/heading and the five-row responsibility table. Validated in commit `a84e90e`. | closed |
| T-01-02 | Spoofing | Scope claims read as implemented capability | high | mitigate | `SCOPE.md` Purpose states the document is not implementation, test, deployment, or robot-system acceptance evidence; `PROJECT.md` retains the subproject-capability evidence comment. | closed |
| T-01-03 | Tampering | Outward-facing `documents/` repository | medium | mitigate | D-03 is recorded in `SCOPE.md`; Task 3 asserted `git -C documents status --porcelain` is empty and fails when git itself errors. | closed |
| T-01-04 | Repudiation | Divergent scope statements across `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` | medium | mitigate | D-01 link-not-copy: Task 2 added only link content to each document plus the PROJECT.md decision row; Task 3 checked each link, rejected duplicated table headers, and checked the decision row. | closed |
| T-01-05 | Spoofing | Portability statements read as support commitments | medium | mitigate | D-12 and D-13 are expressed in the portability table's `Explicitly not guaranteed` column; Linux/amd64 and ARM64 are labeled examples only. | closed |
| T-01-SC | Tampering | npm/pip/cargo installs | low | accept | Not applicable: this documentation-only phase ran no package-manager installs and added no external dependency or lockfile. | closed — accepted risk |

*Status: open · closed · open — below high threshold (non-blocking)*
*Severity: critical > high > medium > low — only open threats at or above workflow.security_block_on count toward threats_open*
*Disposition: mitigate (implementation required) · accept (documented risk) · transfer (third-party)*

---

## Accepted Risks Log

| Risk ID | Threat Ref | Rationale | Accepted By | Date |
|---------|------------|-----------|-------------|------|
| AR-01-01 | T-01-SC | No package-manager installs, external dependencies, lockfiles, or executable code were added; the supply-chain surface is not applicable to this documentation-only phase. | EROS project owner | 2026-11-03 |

*Accepted risks do not resurface in future audit runs.*

---

## Security Audit Trail

| Audit Date | Threats Total | Closed | Open | Run By |
|------------|---------------|--------|------|--------|
| 2026-11-03 | 6 | 6 | 0 | gsd-execute-phase orchestrator (ASVS L1 short-circuit) |

---

## Sign-Off

- [x] All threats have a disposition (mitigate / accept / transfer)
- [x] Accepted risks documented in Accepted Risks Log
- [x] `threats_open: 0` confirmed
- [x] `status: verified` set in frontmatter

**Approval:** verified 2026-11-03
