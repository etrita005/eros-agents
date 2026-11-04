# Phase 2: Implemented Scope and Explicit Exclusions - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-11-03
**Phase:** 2-Implemented Scope and Explicit Exclusions
**Areas discussed:** Capability inventory granularity, Exclusion scope

---

## Capability Inventory Granularity

### Inventory unit

| Option | Description | Selected |
|--------|-------------|----------|
| Capability groups | 6–8 user-visible groups with FR references and evidence. | ✓ |
| FR requirements one by one | One row per FR-01–FR-20. | |
| Architecture components | Launcher, toolchain, adapters, drivers, packaging, runner. | |

**User's choice:** Capability groups. Readability and traceability matter more
than a full FR-by-FR matrix.

### Group count and names

| Option | Description | Selected |
|--------|-------------|----------|
| 8 groups | Controlled environment/entry; Bazel orchestration/toolchain; native C++; CMake; Conan+CMake; runtime closure; Debian/symbol packaging; acceptance tooling. | ✓ |
| 6 groups | Merge build adapters and merge runtime/packaging. | |
| 10 groups | Split packaging and acceptance sub-areas further. | |

**User's choice:** 8 groups. Multi-architecture build configuration is folded
into the environment/build groups rather than becoming a separate group.

### Sub-capability detail

| Option | Description | Selected |
|--------|-------------|----------|
| Group row + keywords | One row per group; 2–4 keywords, FR refs, and evidence per row. | ✓ |
| Sub-capability rows | 20–30 separate rows. | |
| Main table + evidence appendix | Short main table with a separate evidence appendix. | |

**User's choice:** Group row plus keywords. No sub-capability table.

### Inventory scope

| Option | Description | Selected |
|--------|-------------|----------|
| Forge table + exclusion table | Forge capabilities in one table; EROS absent scope in a separate exclusion table. | ✓ |
| Five-area unified matrix | One status matrix for Forge and all future areas. | |
| Forge table only | No separate exclusion table. | |

**User's choice:** Forge table plus a separate EROS exclusion table.

### Partial or unaccepted Forge scope

| Option | Description | Selected |
|--------|-------------|----------|
| Boundary column | Keep the group in the Forge table; record partial/unaccepted scope in an implementation-boundary column. | ✓ |
| Move to exclusion table | Forge table contains only fully implemented groups. | |
| Status + footnote | One partial status per row with footnotes. | |

**User's choice:** Boundary column. Partial Forge scope does not become an EROS
system exclusion.

---

## Exclusion Scope

### Exclusion table unit

| Option | Description | Selected |
|--------|-------------|----------|
| Capability rows | One row per excluded capability/scope with an Area column. | ✓ |
| Area rows | One row per top-level area. | |
| Area sections | Sections with capability rows underneath. | |

**User's choice:** Capability rows with an Area column.

### Exclusion rows

| Option | Description | Selected |
|--------|-------------|----------|
| 9 EROS rows | Runtime; application framework; Linux BSP/hardware support; Zephyr firmware; CI/CD; deployment/rollback; target hardware; target acceptance; OTA/update. | ✓ |
| 6 minimal rows | Only runtime, BSP/hardware, firmware, CI/CD, deployment, target acceptance. | |
| 9 + Forge boundaries | Also include Forge unaccepted scopes. | |

**User's choice:** Nine EROS-scope rows. Forge acceptance gaps stay in the Forge
table's boundary column.

### Status and reason

| Option | Description | Selected |
|--------|-------------|----------|
| Status + reason columns | `Not implemented` / `Not selected` / `Not verified` / `Deferred`, plus a reason. | ✓ |
| Single free-text reason | One flexible reason cell per row. | |
| Category only | One classification label without detailed reason. | |

**User's choice:** Status plus reason columns.

### Exit criterion

| Option | Description | Selected |
|--------|-------------|----------|
| Add exit criterion | State what implementation or evidence would remove the exclusion. | ✓ |
| Future owner only | Name a future phase or v2 without evidence criteria. | |
| None | Keep only status and reason. | |

**User's choice:** Add an `Exit criterion` column.

---

## the agent's Discretion

- Exact wording of capability descriptions and exclusion reasons.
- Stable file/section references for evidence.
- Short introductory paragraphs before each table.
- Markdown formatting, provided the required rows, columns, and status values
  remain explicit.

## Deferred Ideas

- Running Forge acceptance, Docker integration, or target tests during this
  planning phase.
- Re-mapping the Forge codebase before planning.
- Updating `documents/` external documentation.
- Implementing excluded runtime, BSP, firmware, CI/CD, deployment, OTA, or
  Forge expansion work.
- A full FR-by-FR acceptance matrix or per-test status table.
