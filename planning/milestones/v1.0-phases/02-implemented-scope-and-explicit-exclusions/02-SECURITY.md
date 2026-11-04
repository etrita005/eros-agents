---
phase: "2"
slug: "implemented-scope-and-explicit-exclusions"
status: verified
threats_open: 0
asvs_level: 1
created: "2026-11-04"
---

# Phase 2 — Security

> Per-phase security contract: threat register, accepted risks, and audit trail.

---

## Trust Boundaries

| Boundary | Description | Data Crossing |
|----------|-------------|---------------|
| Baseline document to reader | Capability and exclusion statements can be misread as delivered features, test results, support commitments, or permanent non-goals. | Scope status, evidence claims, and limitation wording |
| Forge evidence to EROS baseline | Source paths and Forge documents establish implementation presence only; local test and target acceptance cannot be inferred from them. | FR/NFR references, code paths, document sections |
| Planning repository to `documents/` and `forge/` | The phase must write only the planning baseline and must not alter the outward-facing documentation repository or the Forge subproject. | Repository writes |
| Baseline document to downstream phases | Duplicated or altered exclusion/capability statements would propagate into Phase 3 traceability and consistency review. | Requirement IDs, table content, cross-document links |

---

## Threat Register

| Threat ID | Category | Component | Severity | Disposition | Mitigation | Status |
|-----------|----------|-----------|----------|-------------|------------|--------|
| T-02-01 | Tampering | `agents/planning/SCOPE.md` Phase 1 sections | high | mitigate | Tasks 1 and 2 restricted edits to the insertion points; Task 3's normalized snapshot diff against `/tmp/eros-scope-before.md` showed no change outside the two inserted sections and two Last updated lines. | closed |
| T-02-02 | Spoofing | Forge evidence presented as test or target acceptance | high | mitigate | The evidence guard sentence, per-row boundary column, and no-pass-claim checks keep implementation presence distinct from test and target acceptance. | closed |
| T-02-03 | Tampering | Partial Forge scope moved into the EROS exclusion table | medium | mitigate | Eight Forge groups remain in the Forge table with partial scope in the boundary column; the exclusion table contains exactly the nine EROS rows and only the four allowed statuses. | closed |
| T-02-04 | Repudiation | Exclusions read as permanent non-goals or as delivered scope | medium | mitigate | The milestone-level note is present verbatim after the exclusion table, and no excluded item is described as implemented, selected, verified, or accepted. | closed |
| T-02-05 | Tampering | Writes to `documents/` or `forge/`, or duplicated tables in other planning documents | high | mitigate | Both child repositories report clean worktrees after successful git invocations; each new table header appears exactly once across SCOPE.md, PROJECT.md, REQUIREMENTS.md, and ROADMAP.md. | closed |
| T-02-06 | Spoofing | Ungrounded or nonexistent evidence paths | medium | mitigate | All 25 cited `forge/` paths exist and are tracked in the Forge repository; every Evidence cell combines code and document references; every cited section exists. | closed |
| T-02-SC | Tampering | npm, pip, or cargo installs | low | accept | Not applicable: this documentation-only phase ran no package-manager installs and added no dependency, lockfile, or build step. | closed — accepted risk |

*Status: open · closed · open — below high threshold (non-blocking)*
*Severity: critical > high > medium > low — only open threats at or above workflow.security_block_on count toward threats_open*
*Disposition: mitigate (implementation required) · accept (documented risk) · transfer (third-party)*

---

## Accepted Risks Log

| Risk ID | Threat Ref | Rationale | Accepted By | Date |
|---------|------------|-----------|-------------|------|
| AR-02-01 | T-02-SC | No package-manager installs, external dependencies, lockfiles, or executable code were added; the supply-chain surface is not applicable to this documentation-only phase. | EROS project owner | 2026-11-04 |

*Accepted risks do not resurface in future audit runs.*

---

## Security Audit Trail

| Audit Date | Threats Total | Closed | Open | Run By |
|------------|---------------|--------|------|--------|
| 2026-11-04 | 7 | 7 | 0 | gsd-execute-phase orchestrator (ASVS L1 short-circuit) |

---

## Sign-Off

- [x] All threats have a disposition (mitigate / accept / transfer)
- [x] Accepted risks documented in Accepted Risks Log
- [x] `threats_open: 0` confirmed
- [x] `status: verified` set in frontmatter

**Approval:** verified 2026-11-04
