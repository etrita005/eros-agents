# Phase 1: System Composition and Portability Boundary - Pattern Map

**Mapped:** 2026-11-03
**Files analyzed:** 4 (1 new, 3 modified)
**Analogs found:** 4 / 4 (section-level for `SCOPE.md`; exact self-analogs for link edits)

This is a documentation-only phase. The patterns below are Markdown document
structures, table shapes, citation conventions, and evidence-status wording. No
application code patterns are relevant or invented.

`documents/README.md` is reference-only. D-03 prohibits creating or modifying
`documents/` content in this phase.

## File Classification

| New/Modified File | Role | Data Flow | Closest Analog | Match Quality |
|-------------------|------|-----------|----------------|---------------|
| `agents/planning/SCOPE.md` (new) | planning document / scope baseline | transform (decisions → normative scope text) | `agents/planning/PROJECT.md`; `agents/planning/codebase/ARCHITECTURE.md`; `agents/planning/REQUIREMENTS.md` | partial — no whole-document analog; section-level analogs exist |
| `agents/planning/PROJECT.md` (modified) | planning document / project overview | transform | itself; `agents/planning/REQUIREMENTS.md` | exact (self) |
| `agents/planning/REQUIREMENTS.md` (modified) | planning document / requirements + traceability | transform | itself; `agents/planning/ROADMAP.md` | exact (self) |
| `agents/planning/ROADMAP.md` (modified) | planning document / roadmap + progress | transform | itself | exact (self) |

## Section Coverage

| `SCOPE.md` intended section | Primary analog | Pattern to copy |
|-----------------------------|----------------|-----------------|
| System composition | `agents/planning/codebase/ARCHITECTURE.md` §System Overview | framing sentence + `text` fenced diagram + one-line diagram rule |
| Area responsibilities | `agents/planning/codebase/ARCHITECTURE.md` §Component Responsibilities; `forge/documents/requirements.md` §2 | `Area / Responsibility / Boundary` table |
| Boundaries between areas | `agents/planning/codebase/ARCHITECTURE.md` §Layers; `forge/documents/technical-design.md` §§3.2, 3.4 | per-area responsibility block or adjacent-area boundary table |
| Portability contract | `forge/documents/technical-design.md` §10; `agents/planning/PROJECT.md` §Constraints | `Contract / Rule / Not promised` table |
| `documents/` synchronization rule | `agents/AGENTS.md` §Repository Responsibilities; `agents/planning/codebase/STRUCTURE.md` §`forge/documents/` versus `documents/` | imperative bullet rule |
| Evidence boundary | `agents/AGENTS.md` §Evidence and Acceptance Boundaries; `ARCHITECTURE.md` §Pattern Overview | explicit intended/implemented/verified distinction |

## Pattern Assignments

### `agents/planning/SCOPE.md` (planning document, transform)

**Analog:** `agents/planning/PROJECT.md`,
`agents/planning/codebase/ARCHITECTURE.md`,
`agents/planning/REQUIREMENTS.md`, `forge/documents/requirements.md`,
`forge/documents/test-design.md`

#### Title and metadata pattern

**Analog:** `agents/planning/REQUIREMENTS.md` lines 1-4; `agents/planning/PROJECT.md` lines 1-8 and 114-115.

```markdown
# Requirements: EROS

**Defined:** 2026-11-03
**Core Value:** Provide a unified, verifiable software foundation for robot hardware across supported platforms while keeping build and delivery reproducible.
```

```markdown
# EROS

## What This Is

EROS is a robot operating system project managed as multiple independent Git
repositories with `repo`. The workspace currently contains build and delivery
infrastructure in `forge/`; robot runtime, hardware support, firmware, and
system-level integration are project scope, not implemented capabilities.
```

Closing footer pattern (`REQUIREMENTS.md` lines 74-76, `PROJECT.md` lines 114-115):

```markdown
---

*Requirements defined: 2026-11-03*
*Last updated: 2026-11-03 after roadmap creation*
```

Use a single `#` title, then bold metadata lines, then unnumbered `##` sections.
For `SCOPE.md`, the analog supports `# Scope: EROS` or `# EROS Scope Baseline`
plus `**Defined:** 2026-11-03`. Do not invent a status vocabulary beyond
`Defined` / `Last updated` unless the planner records why.

#### System composition pattern

**Analog:** `agents/planning/codebase/ARCHITECTURE.md` lines 6-37.

````markdown
## System Overview

The checkout is an EROS multi-repository workspace, not an application server. Implemented build and delivery infrastructure lives in `forge/`; `agents/README.md` and `documents/README.md` are repository placeholders. Do not infer robot middleware, service APIs, or application execution architecture from the EROS name.

```text
Host entry / consuming Bazel module
  forge/forge-bazel       forge/forge-shell
             \             /
          forge/forge_container.py
        locked persistent Docker container
                     |
   forge/MODULE.bazel + forge/bazel/bazelrc
                     |
   environment capture + normalized variants
   forge/bazel/toolchain/   forge/bazel/config/
                     |
       +-------------+--------------------+
       |             |                    |
 Native C++       Foreign CMake       Locked Conan+CMake
 runtime.bzl     cmake_forge.bzl     cmake_conan_forge.bzl
```

Diagram rule filenames are relative to `forge/bazel/`; execution belongs to the controlled build environment, not the host toolchain.
````

Copy three structural elements:

1. A short framing paragraph that says what the document describes and what a
   reader must not infer from it.
2. A fenced `text` diagram with explicit layer labels and `|` / `+---+`
   separators. The scanned planning documents use this medium rather than
   Mermaid.
3. A one-line "diagram rule" after the fence that defines path or label
   conventions used by the diagram.

Composition shape template for this phase (five areas, two layers, all names
explicit per D-04):

```text
Robot OS product layer
  Linux BSP | Embedded board firmware | Robot runtime and system services

Supporting infrastructure layer
  Forge | CI/CD
```

Keep the diagram free of API, message-field, or implementation detail (D-14).

#### Responsibility table pattern

**Analog:** `agents/planning/codebase/ARCHITECTURE.md` lines 39-54.

```markdown
## Component Responsibilities

| Component | Responsibility | File |
|-----------|----------------|------|
| Host transport | Discover consuming module; validate image, mounts and state; forward commands/signals | `forge/forge_container.py` |
| Environment verification | Compare actual controlled tools against the reviewed local image contract | `forge/docker/verify_environment.py` |
| Module composition | Register external dependencies, tool repository and C++ toolchains | `forge/MODULE.bazel` |
```

**Analog for one-responsibility-per-role wording:** `forge/documents/requirements.md` lines 16-24.

```markdown
## 2 参与者

| 角色 | 责任 |
| --- | --- |
| Forge 维护者 | 统一构建入口、平台预设、工具校验、规则、配置归一化、导出接口、运行闭包和打包 |
| 环境与 SDK 维护者 | 按执行架构发布固定的官方构建镜像及环境清单，维护编译工具、目标头文件与库，保证编译和部署的 ABI 一致 |
```

Use the following column shape, which adds layer and boundary without copying
Forge implementation paths:

```markdown
| Area | Layer | Responsibility | Boundary |
|------|-------|----------------|----------|
| Linux BSP | Robot OS product | Owns the Linux boot chain: bootloader, kernel, drivers, device tree, and rootfs hardware enablement for the main compute board | Does not own embedded-board application firmware |
| Embedded board firmware | Robot OS product | Zephyr-based firmware for external boards, including motor-control and other real-time embedded boards; implements the Zephyr-side endpoints and real-time behavior | Zephyr is the foundation of the area, not a separate top-level area |
| Robot runtime and system services | Robot OS product | Owns process and service lifecycle, IPC, device-access abstraction, configuration, logging, time synchronization, and the application framework, component model, lifecycle, and package model | Linux-side software platform; not the Linux BSP |
| Forge | Supporting infrastructure | Owns reproducible builds, dependency management, packaging, and artifact production | Does not own pipeline orchestration |
| CI/CD | Supporting infrastructure | Owns pipeline orchestration, test execution, release, deployment, and rollback | Consumes Forge outputs rather than redefining build semantics |
```

The rows above are D-04 through D-08 expressed in the analog's table shape. One
row per area, one responsibility per row, and a boundary column prevent the
table from becoming a feature list.

#### Boundary block pattern

**Analog:** `agents/planning/codebase/ARCHITECTURE.md` lines 67-95.

```markdown
## Layers

**Host/container boundary:**
- Purpose: Provide one reviewed Linux/amd64 execution environment per source workspace.
- Location: `forge/forge_container.py`, `forge/docker/exec_command.py`.
- Contains: Image/state verification, Docker execution, source/Git metadata mounts and signal forwarding.
- Depends on: Host Python, local Docker, `forge/docker/image.lock.json`.
- Used by: `forge/forge-bazel`, `forge/forge-shell`.
```

This is the closest structure for "one responsibility per area" plus explicit
adjacency. For `SCOPE.md`, keep `Purpose`, `Contains`, and `Used by`/`Adjacent
boundary`, but do not copy `Location` code paths: D-14 excludes implementation
design. If a per-area block style is preferred over the responsibility table,
use:

```markdown
**Linux BSP (Robot OS product layer):**
- Purpose: Own the Linux boot chain for the main compute board.
- Contains: Bootloader, kernel, drivers, device tree, and rootfs hardware enablement.
- Adjacent boundary: Does not own embedded-board application firmware.
- Interface to adjacent areas: Provides fieldbus drivers used by the runtime device model (D-09).
```

**Adjacent-area boundary table pattern** — the same section's dependency
language, adapted to D-09 and D-10:

```markdown
| Adjacent areas | Boundary rule | Explicitly not |
|----------------|---------------|----------------|
| Robot runtime and system services ↔ Linux BSP | Runtime defines the Linux-side device model and communication protocol; BSP provides the fieldbus drivers | BSP does not define runtime device semantics |
| Linux BSP/runtime ↔ Embedded board firmware | Linux and external Zephyr boards communicate over fieldbuses such as CAN; firmware implements the Zephyr-side endpoint under its real-time constraints | Not same-SoC AMP |
| Forge ↔ CI/CD | CI/CD consumes Forge build and package outputs | Forge does not own pipeline orchestration; CI/CD does not redefine build semantics |
| System update/OTA | Updates cross the runtime, BSP, and firmware areas | No orchestration, boot-slot, or firmware-recovery owner is assigned in v1 (D-10) |
```

The last row is deliberately a dependency note, not an ownership assignment.

#### Portability contract pattern

**Analog:** `forge/documents/technical-design.md` lines 322-334 (support and
acceptance boundary table); `agents/planning/codebase/ARCHITECTURE.md` line 184
(platform boundary constraint); `agents/planning/PROJECT.md` lines 75-86
(evidence and platform constraints).

```markdown
| 能力 | 当前支持与验收边界 |
| --- | --- |
| 匹配目标 SDK 的内容与来源 | 当前 `sdk_id=missing:/opt/eros`；交叉构建至多形成构建/ELF 证据；编译与部署 glibc/loader 匹配、目标运行及发布仍需单独取证，不降低 D02 |
| 官方可获取镜像与升级 | 当前只锁本地 Linux/amd64 镜像 ID，无 registry、ARM64 原生镜像或完整可重建发布清单；APT 包精确来源和多架构平台摘要需审核 |
```

```markdown
- **Platform boundary:** Local launchers explicitly require the Linux/amd64 baseline. Four registered toolchains do not establish four validated host environments (`forge/forge_container.py:117`, `forge/MODULE.bazel:15`).
```

```markdown
- **Evidence boundary**: Requirements, design documents, implementation, test
  results, and target acceptance are distinct states.
```

Adapt the table to a normative contract with a `Not promised` column. This
matches the analog's "current support and acceptance boundary" separation while
stating the D-11 to D-13 rules:

```markdown
| Contract | Rule | Explicitly not guaranteed |
|----------|------|---------------------------|
| Common source and public APIs | Robot runtime and system services, including the application framework, keep common source and public APIs across supported platforms where practical | Identical internal implementations across platforms |
| Platform-specific implementation | Linux BSP and embedded firmware may have platform-specific implementations behind those boundaries | A single shared binary for all platforms |
| Build model | Each platform is rebuilt for its target | Identical binaries across platforms; additional cross-version ABI compatibility |
| Named examples | Linux/amd64 is the current development-host example; ARM64 is a candidate target example | A support commitment or hardware selection |
```

Do not cite Forge's `sdk_id` or image-lock values as EROS-wide support
commitments; those are Forge-specific evidence boundaries.

#### `documents/` synchronization rule pattern

**No direct analog.** The closest structural patterns are repository
responsibility bullets and the documentation-boundary note.

**Analog:** `agents/AGENTS.md` lines 13-25.

```markdown
## Repository Responsibilities

- `forge/`: Build and delivery infrastructure. Provides a controlled container
  environment, Bazel build orchestration, package-internal CMake builds, Conan
  dependency management, runtime artifact packaging, Debian package generation,
  and build acceptance tooling.
- `documents/`: Project-level documentation repository. Consult its existing
  documentation before adding or duplicating project-wide material.
- `agents/`: Shared agent instructions and project-level planning artifacts.
```

**Analog:** `agents/planning/codebase/STRUCTURE.md` lines 124-127.

```markdown
**`forge/documents/` versus `documents/`:**
- Purpose: Forge architecture and API documentation lives in `forge/documents/`; the inspected top-level documentation repository is a placeholder.
- Key files: `forge/documents/README.md`, `forge/documents/technical-design.md`, `forge/documents/requirements.md`, `forge/documents/test-design.md`, `documents/README.md`.
- Distinguish design goals in `forge/documents/technical-design.md` from implemented rules and acceptance evidence.
```

Record the rule as an imperative boundary block, not as a planning table:

```markdown
## Documentation Boundary

- `documents/` is the outward-facing project documentation repository for
  architecture introductions, core-technology explanations, and similar
  material. It is not the planning baseline.
- After robot-OS functionality is implemented or changed, update the corresponding
  `documents/` material in the same delivery cycle.
- This phase does not create or modify `documents/` content. This rule is
  recorded here for later phases.
```

This content comes from D-02 and D-03. The repository's established style for
such rules is a short bold or backticked lead-in followed by an explicit `Do
not` / `must` statement (`AGENTS.md` lines 23-25, 53-62).

#### Evidence-boundary pattern

**Analog:** `agents/AGENTS.md` lines 53-62.

```markdown
## Evidence and Acceptance Boundaries

- Distinguish intended requirements, implemented behavior, and verified results.
  Design documents alone are not implementation or acceptance evidence.
- A successful local build or cross-compilation does not establish deployment,
  target ABI compatibility, or acceptance on a robot system.
- Subproject verification does not automatically establish EROS system-level
  acceptance. State the actual environment, target, checks, and limitations.
- Keep passed, failed, skipped, blocked, and unverified results distinct. Do not
  present missing prerequisites or unexecuted checks as successful validation.
```

**Analog:** `agents/planning/codebase/ARCHITECTURE.md` line 65.

```markdown
- Separate implementation from acceptance claims: remote image publication, ARM64-host support and target deployment acceptance are not established merely by recipes or cross-build rules (`forge/README.md`, `forge/documents/technical-design.md`).
```

**Analog:** `forge/documents/test-design.md` lines 11-18 defines the execution
status table:

```markdown
| 状态 | 含义 |
| --- | --- |
| PASS | 对明确范围的全部行为断言通过 |
| FAIL | 退出码、诊断、制品或行为不符合预期 |
| SKIP | 该范围不适用于所选平台 |
| BLOCKED | 环境或外部执行/取证前置条件不具备 |
```

Use one document-level evidence note in `SCOPE.md`:

```markdown
This document defines intended system composition and scope. It is not
implementation, test, deployment, or robot-system acceptance evidence.
```

Do not add `PASS`/`FAIL`/`SKIP`/`BLOCKED` columns to `SCOPE.md`. Those are
acceptance-execution states from `forge/documents/test-design.md`, not statuses
of a scope baseline. If the planner needs an evidence status later, use the
three-way distinction from `AGENTS.md` (intended requirements, implemented
behavior, verified results) and the `Pending` traceability vocabulary in
`REQUIREMENTS.md` lines 60-67.

#### Cross-reference and file-path citation pattern

**Analog:** `01-CONTEXT.md` lines 106-141 (canonical reference list);
`agents/AGENTS.md` lines 64-74 (documentation navigation).

```markdown
### Project and Planning

- `.planning/PROJECT.md` — EROS identity, core value, active requirements, and
  constraints.
- `.planning/REQUIREMENTS.md` — `SCOPE-01` and `SCOPE-04` define this phase.
- `.planning/ROADMAP.md` — Phase 1 goal, dependencies, and success criteria.
```

```markdown
For Forge work, read `forge/AGENTS.md` and the relevant authoritative documents:

- `forge/README.md`: Supported environment, launchers, and consumer setup.
- `forge/documents/README.md`: Documentation index.
- `forge/documents/requirements.md`: Forge requirements and scope.
```

Planning documents use backticked workspace-relative paths with an em dash or
colon, one citation per line. They do not use inline Markdown links for internal
planning references. `PROJECT.md` line 68 follows the same convention:

```markdown
- The codebase map in `.planning/codebase/` records the implemented Forge
  architecture, conventions, testing, integrations, and concerns.
```

`SCOPE.md` should therefore cite `.planning/PROJECT.md`,
`.planning/REQUIREMENTS.md`, `.planning/ROADMAP.md`, `forge/AGENTS.md`,
`documents/README.md`, and other workspace files as backticked paths. Do not
switch to relative Markdown links merely because `forge/documents/` uses them;
that is a different documentation repository with a different citation style.
D-01 names the physical owning path `agents/planning/SCOPE.md`; the planning
corpus cites the `.planning/` navigation path. Use one form consistently within
`SCOPE.md` itself.

#### Scope-ownership and link-not-copy pattern

**Analog:** `agents/planning/PROJECT.md` lines 88-95.

```markdown
## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Initialize GSD at the EROS workspace root | Cross-repository scope and subproject boundaries need one project-level baseline | — Pending |
| Keep Forge as a subproject, not the EROS roadmap | Forge is build and delivery infrastructure, not the whole robot operating system | — Pending |
```

Add one row when recording D-01:

```markdown
| Keep the scope baseline in `agents/planning/SCOPE.md` | One authoritative composition baseline avoids divergent copies in `PROJECT.md`, `REQUIREMENTS.md`, and `ROADMAP.md` | — Pending |
```

Keep the `— Pending` outcome until phase verification supplies evidence for a
different outcome. Do not mark the decision complete merely because the file
exists.

---

### `agents/planning/PROJECT.md` (planning document, transform)

**Analog:** itself

**Link-insertion pattern** (from `## Context`, lines 62-74):

```markdown
## Context

- The workspace root is an EROS multi-repository workspace, not an application
  repository.
- `forge/` is the only subproject with substantial implementation. `agents/` and
  `documents/` currently contain repository scaffolding.
- The codebase map in `.planning/codebase/` records the implemented Forge
  architecture, conventions, testing, integrations, and concerns.
- The project-level planning artifacts are owned by the `agents` repository
  through the `.planning/` symlink.
```

Add a single link sentence or bullet such as:

```markdown
- The authoritative system composition and portability baseline is
  `.planning/SCOPE.md`; this document records project-level status rather than
  duplicating that baseline.
```

**Decision-table pattern** (lines 88-95): use the existing `Decision / Rationale /
Outcome` row shape for D-01, as shown above. Do not add a new composition or
portability table to `PROJECT.md`; D-01 requires links rather than duplication.

**Evidence-status pattern** (lines 17-31): preserve the `### Validated` /
`### Active` / `### Out of Scope` structure and the HTML comment:

```markdown
<!-- Existing capabilities established by the codebase map. These are
subproject capabilities, not EROS system-level acceptance. -->
```

That comment is the established guard against reading Forge capabilities as
EROS system acceptance. Do not remove or weaken it.

---

### `agents/planning/REQUIREMENTS.md` (planning document, transform)

**Analog:** itself

**Requirement wording pattern** (lines 10-15):

```markdown
### Scope

- [ ] **SCOPE-01**: Define the long-term EROS system composition and the responsibility of each composition area.
- [ ] **SCOPE-04**: Define the portability boundary: common source and APIs where practical, platform-specific builds where required, and no guarantee of identical binaries across platforms.
```

**Traceability table pattern** (lines 56-67):

```markdown
## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| SCOPE-01 | Phase 1 | Pending |
| SCOPE-02 | Phase 2 | Pending |
| SCOPE-03 | Phase 2 | Pending |
| SCOPE-04 | Phase 1 | Pending |
| TRACE-01 | Phase 3 | Pending |
| REVIEW-01 | Phase 3 | Pending |
```

Add the `SCOPE.md` link in the `## v1 Requirements` introduction (lines 6-8),
not inside individual requirement bullets:

```markdown
Requirements for the initial scope baseline. Each maps to a roadmap phase.
System composition and portability detail is defined in `.planning/SCOPE.md`.
```

Do not duplicate the five-area composition, boundary table, or portability
contract here. The traceability table remains the status authority. Keep
`Pending` unless phase execution has produced evidence for a different status;
do not use `PASS` because documentation completion is not acceptance execution.

---

### `agents/planning/ROADMAP.md` (planning document, transform)

**Analog:** itself

**Phase-detail label pattern** (lines 21-31):

```markdown
### Phase 1: System Composition and Portability Boundary
**Goal:** Readers can see the long-term EROS system composition, the responsibility of each area, and the platform portability contract in the baseline documents.
**Mode:** mvp
**Depends on:** Nothing (first phase)
**Requirements:** SCOPE-01, SCOPE-04
**Success Criteria** (what must be TRUE):
  1. Every long-term composition area of EROS is listed with exactly one stated responsibility, and no listed area lacks an assigned responsibility.
  2. The composition distinguishes EROS system areas from build and delivery infrastructure, so a reader can identify Forge's role without treating Forge as the whole operating system.
  3. The portability rule is recorded for platform-sensitive areas: common source and APIs where practical, platform-specific builds where required.
  4. The documents state explicitly that identical binaries are not guaranteed across platforms.
**Plans**: TBD
```

Add a bold label line matching the existing labels:

```markdown
**Scope baseline:** `.planning/SCOPE.md`
```

Use this instead of copying the composition or portability tables into the
roadmap. Keep the `**Success Criteria** (what must be TRUE)` phrasing and numbered
criteria; they are the established acceptance wording for this project.

**Progress table pattern** (lines 63-67):

```markdown
| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. System Composition and Portability Boundary | 0/TBD | Not started | - |
```

Update only the plan count, status, and completion fields that the planning and
execution workflow actually changes. Do not claim completion from document
creation alone.

## Shared Patterns

### Single source of truth via links

**Source:** `01-CONTEXT.md` D-01 (lines 22-26); `PROJECT.md` `## Key Decisions`
(lines 88-95).

`SCOPE.md` owns the composition, responsibility, boundary, portability, and
`documents/` synchronization content. `PROJECT.md`, `REQUIREMENTS.md`, and
`ROADMAP.md` add references to it and do not repeat its tables. Any duplicate
table is a consistency risk that Phase 3 would have to reconcile.

### Heading hierarchy

**Source:** `PROJECT.md` lines 1-115; `REQUIREMENTS.md` lines 1-76;
`ROADMAP.md` lines 1-67.

Planning documents use `#` title, unnumbered `##` sections, and `###`
sub-sections. `forge/documents/requirements.md` uses numbered headings such as
`## 1 核心契约`; that is a Forge documentation convention, not the planning
convention. `SCOPE.md` should use unnumbered `##` headings so it matches the
planning corpus.

### File-path citation style

**Source:** `01-CONTEXT.md` lines 106-141; `AGENTS.md` lines 64-74;
`PROJECT.md` line 68.

Use backticked workspace-relative paths, with a colon or em dash explaining the
referent. Planning documents do not use relative Markdown links for internal
references. Note that `.planning/` is a symlink to `agents/planning/`; cite the
navigation path consistently rather than alternating between the symlink and its
target in one document.

### Evidence vocabulary

**Source:** `AGENTS.md` lines 53-62; `ARCHITECTURE.md` line 65;
`STATE.md` line 64.

Use the three-way distinction: intended requirements, implemented behavior, and
verified results. A scope document records intended composition and boundaries.
It does not establish implementation, deployment, target ABI compatibility, or
robot-system acceptance. Do not import the Forge acceptance states
(`PASS`/`FAIL`/`SKIP`/`BLOCKED`) into `SCOPE.md`; those describe executed checks,
not document status.

### Table conventions

**Source:** `ARCHITECTURE.md` lines 41-54; `REQUIREMENTS.md` lines 43-54 and
60-67; `forge/documents/requirements.md` lines 18-24 and 28-57.

Use a header row, a separator row, and one record per row. Keep identifier
columns first when identifiers exist (`Requirement`, `编号`, `Phase`). Add a
`Boundary` or `Not promised` column rather than mixing normative and
non-normative statements in a single prose cell. The repository accepts both
compact (`|---|---|`) and padded (`|-----------|----------------|`) separators;
match the nearest document rather than reformatting existing tables.

### Imperative boundary statements

**Source:** `AGENTS.md` lines 19-25 and 53-62; `ARCHITECTURE.md` lines 178-186;
`forge/documents/technical-design.md` lines 310-334.

Boundaries are stated with explicit ownership and negation: "owns", "does not
own", "must", "do not", "not promised", "not established". Avoid soft phrasing
such as "may generally" or "should typically" for a normative baseline. This is
the dominant style in the existing project instructions and evidence sections.

### Scope depth guard

**Source:** `01-CONTEXT.md` D-14 (lines 88-90); `forge/documents/requirements.md`
lines 1-5.

`SCOPE.md` documents purpose, one responsibility per area, boundaries between
adjacent areas, and the portability contract. It does not define APIs, message
fields, interface signatures, or implementation designs. The Forge requirements
document makes the same separation explicit: interface descriptions and a
successful build do not replace acceptance evidence.

### Language

**Source:** `01-CONTEXT.md` the agent's Discretion (lines 92-97);
`PROJECT.md` and `REQUIREMENTS.md` throughout.

Write `SCOPE.md` in English to match the planning corpus. `forge/documents/`
uses Chinese prose; copy its table structures and boundary discipline, not its
language or its Forge-specific content.

## No Analog Found

| File / section | Role | Data Flow | Reason |
|----------------|------|-----------|--------|
| Whole-document `SCOPE.md` structure | planning document / scope baseline | transform | No project-level scope baseline exists; compose from section-level analogs in `PROJECT.md`, `ARCHITECTURE.md`, and `REQUIREMENTS.md` |
| Two-layer, five-area system composition | planning document section | transform | No existing document defines EROS system areas; `ARCHITECTURE.md` documents Forge code architecture only. Source content is D-04 through D-10 in `01-CONTEXT.md` |
| `documents/` synchronization rule | planning document section | transform | `documents/README.md` is a one-line placeholder; no existing rule text. Source content is D-02 and D-03, with imperative style from `AGENTS.md` |
| Project-level portability contract | planning document section | transform | No dedicated analog; `forge/documents/technical-design.md` §10 is Forge-specific support-boundary evidence, not an EROS-wide compatibility promise. Source content is D-11 through D-13 |
| Scope-baseline evidence-status convention | planning document convention | transform | Existing `PASS`/`FAIL`/`SKIP`/`BLOCKED` is acceptance-execution vocabulary; use the intended/implemented/verified distinction from `AGENTS.md` instead |

## Metadata

**Analog search scope:** `agents/planning/`, `agents/planning/codebase/`,
`forge/documents/`, `documents/`, and workspace-root `AGENTS.md`.

**Files scanned:** 15 — `01-CONTEXT.md`, `PROJECT.md`, `REQUIREMENTS.md`,
`ROADMAP.md`, `STATE.md`, `AGENTS.md`, `ARCHITECTURE.md`, `STRUCTURE.md`,
`CONVENTIONS.md`, `CONCERNS.md`, `forge/documents/requirements.md`,
`forge/documents/test-design.md`, `forge/documents/technical-design.md`,
`documents/README.md`, `agents/README.md`.

**Tracked-source gate:** All analog paths were verified with `git ls-files` in
their owning repository. `agents/planning/*` is tracked by the `agents`
repository; `forge/documents/*` is tracked by the `forge` repository;
`documents/README.md` is tracked by the `documents` repository. `.planning/` is a
symlink to `agents/planning/`; no gitignored install or runtime mirror was used.

**Pattern extraction date:** 2026-11-03
