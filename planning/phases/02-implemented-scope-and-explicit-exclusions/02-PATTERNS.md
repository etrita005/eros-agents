# Phase 2: Implemented Scope and Explicit Exclusions - Pattern Map

**Mapped:** 2026-11-04
**Files analyzed:** 1 (1 modified)
**Analogs found:** 1 / 1 whole-file (exact self); 5 / 5 section-level analogs for the two new tables

This is a documentation-only phase. The patterns below are Markdown section
placement, table shapes, citation conventions, evidence-status wording, and
boundary phrasing. No application code patterns are relevant or invented.

`documents/` is reference-only. D-02 and the Phase 1 documentation-boundary rule
prohibit creating or modifying `documents/` content.

## File Classification

| New/Modified File | Role | Data Flow | Closest Analog | Match Quality |
|-------------------|------|-----------|----------------|---------------|
| `agents/planning/SCOPE.md` (modified) | planning document / scope baseline | transform (locked decisions + Forge evidence → normative capability and exclusion tables) | itself, lines 1-72; Forge evidence tables in `forge/documents/requirements.md`, `forge/documents/technical-design.md`, and `agents/planning/codebase/ARCHITECTURE.md` | exact (self) for document structure; role-match for table content |

## Section Coverage

| `SCOPE.md` intended section | Primary analog | Pattern to copy |
|-----------------------------|----------------|-----------------|
| `## Implemented Forge Capabilities` | `forge/documents/requirements.md` §5-§6; `forge/documents/technical-design.md` §10; `agents/planning/codebase/ARCHITECTURE.md` §Component Responsibilities; `forge/README.md` §Public Interfaces | One row per capability group, FR/NFR reference column, repository-relative path evidence, explicit boundary column |
| `## Explicit Exclusions` | `agents/planning/REQUIREMENTS.md` §Out of Scope; `agents/planning/PROJECT.md` §Out of Scope; `agents/planning/codebase/CONCERNS.md` gap blocks; `ROADMAP.md` success criteria | Five-column table with a small closed status vocabulary, per-row reason, and actionable exit criterion |

## Pattern Assignments

### `agents/planning/SCOPE.md` (planning document, transform)

**Analog:** itself, plus the section-level Forge document analogs listed below.

#### Section placement and metadata pattern

**Analog:** `agents/planning/SCOPE.md` lines 49-62; metadata pattern at lines 1-4,
69-72.

Current order to preserve:

```markdown
## Portability Contract

| Contract | Rule | Explicitly not guaranteed |
...
| Named examples | Linux/amd64 is the current development-host example; ARM64 is a candidate target example. | A support commitment or hardware selection. |

## Documentation Boundary
```

Insert the two new `##` sections between `## Portability Contract` (ends at line
56) and `## Documentation Boundary` (starts at line 58), in this order:

```markdown
## Implemented Forge Capabilities

[table and optional framing paragraph]

## Explicit Exclusions

[table and required milestone-level note]

## Documentation Boundary
```

Do not renumber or reword the existing Phase 1 sections. Keep unnumbered `##`
headings; `forge/documents/` uses numbered headings such as `## 5 功能需求`, and
that is a Forge documentation convention, not the planning convention.

Update `**Last updated:** 2026-11-03` at line 4. The closing footer line 72 also
carries a `*Last updated:*` date; the planning corpus updates both metadata and
footer together (`REQUIREMENTS.md` lines 76-77, `PROJECT.md` lines 119-120), so
align both lines to the same new date rather than leaving two dates in one file.
Leave `**Defined:** 2026-11-03` unchanged.

#### Forge capability table — column shape and row granularity

**Analog:** `forge/documents/requirements.md` lines 86-109 (FR table) and lines
111-118 (NFR table):

```markdown
| 编号 | 需求 | 验收要求 | 决策依据 |
| --- | --- | --- | --- |
| FR-01 | 固定环境与 SDK | 校验实际工具和 SDK 内容符合已发布环境身份，目标头文件、链接库、glibc 与加载器一致；不匹配时失败 | D00-D02 |
| FR-02 | 平台预设 | 正确选择执行工具链、目标 CPU 与 ABI，拒绝不支持的预设组合 | D00-D01 |
```

```markdown
| 编号 | 要求 | 判定 |
| --- | --- | --- |
| NFR-01 | 可复现语义与可信缓存 | 同一声明输入具有相同构建语义，配置/SDK 变化不能错误复用；不承诺任意第三方构建天然逐字节一致 |
```

**Analog:** `forge/documents/technical-design.md` lines 326-332 (capability
versus current support/acceptance boundary):

```markdown
| 能力 | 当前支持与验收边界 |
| --- | --- |
| 匹配目标 SDK 的内容与来源 | 当前 `sdk_id=missing:/opt/eros`，交叉构建至多形成构建/ELF 证据；编译与部署 glibc/loader 匹配、目标运行及发布仍需单独取证，不降低 D02 |
| 环境/入口验收 | 独立入口用例检查镜像身份、挂载、服务端复用、单 PID 中断和工具篡改；同一 checkout 不支持多实例并行。TC-ENV-05..07、TC-ENTRY-01..05、TC-CI-03 未完整接入 runner，复杂信号/并发、CI 与独立机器缓存须分别取证 |
```

**Analog:** `agents/planning/codebase/ARCHITECTURE.md` lines 41-54
(component / responsibility / path table):

```markdown
| Component | Responsibility | File |
|-----------|----------------|------|
| Host transport | Discover consuming module; validate image, mounts and state; forward commands/signals | `forge/forge_container.py` |
| Environment verification | Compare actual controlled tools against the reviewed local image contract | `forge/docker/verify_environment.py` |
```

Use exactly the D-05 column shape and eight D-04 rows:

```markdown
| Capability group | Implemented sub-capabilities | Forge requirement refs | Evidence | Implementation boundary / not covered |
|------------------|------------------------------|------------------------|----------|---------------------------------------|
| Controlled build environment and entry | ... | FR-19, FR-20 | `forge/forge_container.py`; `forge/documents/requirements.md` §3.1 | ... |
```

Table-shape rules grounded in the analogs:

- One row per capability group, eight rows total. Do not expand into the 20-row
  FR table or a component-by-component architecture dump (D-04, D-05).
- Each `Implemented sub-capabilities` cell holds 2-4 short keywords separated by
  commas or semicolons, not a nested table and not prose paragraphs.
- `Forge requirement refs` uses the exact identifiers `FR-01` through `FR-20` and
  `NFR-01` through `NFR-04`, comma-separated in ascending order. The analog uses
  the same identifier-first convention (`requirements.md` lines 88-109,
  113-118; `test-design.md` lines 76, 230-253).
- Cite FR/NFR IDs only where applicable; do not invent an ID or a mapping. If a
  row has no direct FR, cite the governing technical decision (`D01`, `D16`) as
  `requirements.md` does in its `决策依据` column.
- `Evidence` combines at least one repository-relative code path with one
  authoritative Forge document, per D-07. Prefer stable section references over
  line numbers; `ARCHITECTURE.md` line 65 and `CONCERNS.md` use the same
  path-plus-document style.
- `Implementation boundary / not covered` is the structural counterpart of
  `Explicitly not guaranteed` in `SCOPE.md` lines 51-56 and `当前支持与验收边界`
  in `technical-design.md` lines 326-332. Keep partial, unaccepted, and
  environment-limited scope in this column; do not move a partial Forge
  capability into the exclusion table (D-06).

Suggested row-to-evidence mapping (the planner should verify against the cited
sections, not add new claims):

| Capability group (D-04) | Primary code evidence | Authoritative document evidence | Requirement refs |
|-------------------------|-----------------------|---------------------------------|------------------|
| Controlled build environment and entry | `forge/forge_container.py`, `forge/forge-bazel`, `forge/forge-shell`, `forge/docker/verify_environment.py`, `forge/docker/image.lock.json` | `forge/README.md` §Environment And Setup; `forge/documents/requirements.md` §3.1; `forge/documents/technical-design.md` §3 | FR-01, FR-19, FR-20; NFR-02, NFR-04 |
| Bazel orchestration and toolchain | `forge/MODULE.bazel`, `forge/bazel/bazelrc`, `forge/bazel/toolchain/host_tools.bzl`, `forge/bazel/config/validation.bzl`, `forge/bazel/toolchain/generate_files.py` | `forge/documents/requirements.md` §4; `forge/documents/technical-design.md` §2-§3; `forge/README.md` §Platforms And ABI | FR-02, FR-10, FR-19; NFR-01 |
| Native C++ build path | `forge/bazel/runtime.bzl` | `forge/documents/requirements.md` §5; `forge/documents/technical-design.md` §6; `forge/examples/README.md` lessons 01-02 | FR-05, FR-15; NFR-01 |
| Foreign CMake integration | `forge/bazel/cmake_forge.bzl` | `forge/documents/cmake-forge.md`; `forge/documents/technical-design.md` §5; `forge/examples/README.md` lesson 03 | FR-05, FR-10, FR-15, FR-18 |
| Locked Conan + CMake integration | `forge/bazel/cmake_conan_forge.bzl`, `forge/bazel/tools/conan_action.py`, `forge/bazel/tools/cmake_action.py` | `forge/documents/requirements.md` §3 D04-D05, D07, D25; `forge/documents/cmake-conan-forge.md`; `forge/documents/technical-design.md` §4-§5 | FR-03, FR-04, FR-10, FR-18; NFR-02 |
| Runtime closure | `forge/bazel/runtime.bzl`, `forge/bazel/deb.bzl`, `forge/bazel/packaging/driver.py` | `forge/documents/technical-design.md` §6; `forge/documents/deb-packaging.md`; `forge/examples/README.md` lesson 05 | FR-06, FR-07, FR-13, FR-15 |
| Debian and symbol packaging | `forge/bazel/deb.bzl`, `forge/bazel/packaging/driver.py`, `forge/bazel/toolchain/export_runtime.sh` | `forge/documents/requirements.md` §5 FR-11, FR-16, FR-17 and §3 D11, D13, D22-D24; `forge/documents/technical-design.md` §7-§8; `forge/examples/README.md` lessons 06, 06b, 07 | FR-11, FR-16, FR-17; NFR-01 |
| Acceptance and verification tooling | `forge/tests/test_runner.py`, `forge/tests/acceptance_cases.py`, `forge/bazel/toolchain/environment_test.py`, `forge/bazel/packaging/driver_test.py` | `forge/documents/test-design.md` §1 and §6; `forge/tests/README.md` §Results And Evidence; `forge/AGENTS.md` §Verification | FR-14, FR-17, FR-19; NFR-03 |

Every row must carry the applicable D-08 boundary. Source wording to adapt:

```markdown
- Local entry and build flow are Linux/amd64 only; ARM64 native execution is not validated.
- Acceptance registry coverage is incomplete: `TC-ENV-05`-`TC-ENV-07`, `TC-ENTRY-01`-`TC-ENTRY-05`, and `TC-CI-03` are not fully registered.
- ARM64 native execution, registry multi-architecture images, a matching target SDK, target execution, remote-cache evidence, and complete CI acceptance are not established.
- `--all --strict` is not by itself proof of complete release acceptance.
```

These phrases are supported by `forge/README.md` lines 13-14 and 157-162,
`forge/documents/test-design.md` line 5, `forge/tests/README.md` lines 31-32 and
136-138, and `agents/planning/codebase/CONCERNS.md` lines 7-12. Do not upgrade
any of them to "verified", "accepted", "complete", or "PASS".

#### EROS exclusion table — column shape and status vocabulary

**Analog:** `agents/planning/REQUIREMENTS.md` lines 44-55 (two-column Out of
Scope table):

```markdown
## Out of Scope

Explicitly excluded from this scope baseline. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Robot runtime or middleware implementation | This milestone defines scope only; no runtime exists in the workspace |
| BSP, device driver, or firmware implementation | No target hardware or board support is selected or validated |
```

**Analog:** `agents/planning/PROJECT.md` lines 49-63 (reason-after-dash bullets):

```markdown
### Out of Scope

<!-- Milestone-level exclusions. These are project scope, not permanent
anti-features. -->

- Implementing a robot runtime, middleware, or application framework — this
  milestone establishes scope only.
- Implementing BSPs, board firmware, device drivers, or hardware support — no
  target platform is selected or validated here.
```

**Analog for reason + actionable follow-up:** `agents/planning/codebase/CONCERNS.md`
lines 7-12 uses `Issue / Files / Impact / Fix approach / Evidence boundary` and
ends with an explicit limitation statement. `ROADMAP.md` lines 50-55 shows the
success-criterion style to echo in `Exit criterion`.

Use exactly the D-09 column shape and nine rows:

```markdown
| Excluded capability | Area | Status | Reason | Exit criterion |
|---------------------|------|--------|--------|----------------|
| Robot runtime and system services implementation | Robot runtime and system services | Not implemented | No runtime source exists in this workspace; the area is defined only as intended composition | An approved runtime implementation exists with implementation evidence and its own acceptance scope |
```

Table-shape rules:

- Use exactly these status values (D-10): `Not implemented`, `Not selected`,
  `Not verified`, `Deferred`. Capitalize as shown.
- Do not use `partial` as an exclusion status. Partial Forge scope belongs in the
  Forge table's boundary column (D-06, D-10).
- Do not import `PASS` / `FAIL` / `SKIP` / `BLOCKED` from
  `forge/documents/test-design.md` lines 11-16 or `forge/tests/README.md` lines
  129-134. Those are executed-acceptance states; this table records scope status.
- `Area` reuses the five area names from `SCOPE.md` §System Composition and
  §Area Responsibilities, or `Cross-cutting` where D-09 says so.
- `Reason` states why the item is absent from this workspace, in the style of
  `REQUIREMENTS.md` §Out of Scope: one factual reason, no speculative roadmap.
- `Exit criterion` states the implementation or evidence that would remove the
  exclusion, following the `Fix approach` discipline in `CONCERNS.md` line 11.
  Make it verifiable: for example an approved runtime implementation, a selected
  target board with BSP and firmware, or target execution evidence.
- After the table, add the required D-12 note. Use the guard-comment tone of
  `PROJECT.md` lines 51-52 and line 46 of `REQUIREMENTS.md`:

```markdown
These are milestone-level exclusions, not permanent non-goals. They do not
redefine the long-term composition in the sections above.
```

#### Evidence and citation pattern

**Analog:** Phase 1 `01-PATTERNS.md` lines 373-389; `agents/AGENTS.md` lines
64-74; `PROJECT.md` lines 71-77.

Planning documents cite backticked workspace-relative paths with a colon or em
dash, one citation per line or cell. They do not use relative Markdown links for
internal planning references. Cite the Forge evidence as a path plus a stable
section label, for example:

```markdown
`forge/forge_container.py`; `forge/documents/requirements.md` §3.1
```

Do not use a relative link such as `[requirements](../forge/documents/requirements.md)`;
that link style belongs to `forge/documents/`, a different repository. Line
numbers are optional; prefer section names because Forge documents change.
`ARCHITECTURE.md` lines 43-54 and `CONCERNS.md` lines 9, 16, 31 demonstrate the
backticked-path convention.

Tests may be cited only as existing test code or documented test design, never as
a passing result. `forge/documents/test-design.md` line 5 says the design and a
runnable command are not execution evidence; `forge/tests/README.md` lines
129-138 distinguishes state from exit status. `CONCERNS.md` lines 186-187 states
the inspection boundary. Apply the same distinction in the evidence column.

#### Boundary and limitation wording

**Analog:** `agents/AGENTS.md` lines 53-62:

```markdown
- Distinguish intended requirements, implemented behavior, and verified results.
  Design documents alone are not implementation or acceptance evidence.
- A successful local build or cross-compilation does not establish deployment,
  target ABI compatibility, or acceptance on a robot system.
- Keep passed, failed, skipped, blocked, and unverified results distinct. Do not
  present missing prerequisites or unexecuted checks as successful validation.
```

**Analog:** `forge/README.md` lines 33-37 and 157-162; `technical-design.md`
lines 318-320 and 332-334; `CONCERNS.md` lines 10-12, 156-163.

Use explicit negation and state the actual boundary: "local Linux/amd64 only",
"not established", "not validated", "not fully registered", "partial", "not
covered". Avoid soft claims such as "generally supported" or "mostly complete".
Do not equate a design document, recipe, example, cross-build, or ELF inspection
with target acceptance (D-13, D-14).

## Shared Patterns

### Single source of truth via links

**Source:** `02-CONTEXT.md` D-01 and D-03; `PROJECT.md` lines 71-77; Phase 1
`01-PATTERNS.md` lines 549-558.

`SCOPE.md` owns the capability and exclusion tables. `PROJECT.md`,
`REQUIREMENTS.md`, and `ROADMAP.md` continue to link to `.planning/SCOPE.md` and
must not duplicate the tables. Only status/traceability edits required by phase
completion may touch them.

### Heading hierarchy

**Source:** `SCOPE.md` lines 14-67; `PROJECT.md` and `REQUIREMENTS.md` throughout;
`forge/documents/requirements.md` numbered headings.

Use unnumbered `##` sections in `SCOPE.md`. Do not copy the Forge numbering or
Chinese headings. Keep the two new sections as peers of `## Portability
Contract` and `## Documentation Boundary`.

### Table conventions

**Source:** `SCOPE.md` lines 32-56; `ARCHITECTURE.md` lines 41-54;
`forge/documents/requirements.md` lines 88-118; `REQUIREMENTS.md` lines 48-55.

Use a header row, a separator row, and one record per row. Keep identifier-like
columns first where they exist. `SCOPE.md` already uses padded separators
(`|------|-------|`); match that style rather than reformatting the file to
compact separators. Keep one row per capability group or exclusion; do not merge
rows to shorten the table.

### Evidence vocabulary

**Source:** `agents/AGENTS.md` lines 53-62; `forge/documents/test-design.md` line
5; `forge/tests/README.md` lines 129-138; `CONCERNS.md` lines 186-187.

Use three states consistently: intended requirement or design, implemented
behavior, verified result. The Forge table's evidence column describes
implementation presence, not test success. The exclusion table uses the D-10
vocabulary, not the acceptance-runner vocabulary. Never write that a suite
"passes" unless a result artifact is cited (D-14).

### Imperative boundary statements

**Source:** `agents/AGENTS.md` lines 9-11, 53-62; `SCOPE.md` lines 34-56;
`CONCERNS.md` lines 10-12.

Prefer "implements", "provides", "does not implement", "is not selected", "is not
verified", "is deferred", "is not established". State what is not covered in the
same row as the capability so a reader cannot infer completeness from the
capability name alone.

### Language

**Source:** `02-CONTEXT.md`; Phase 1 `01-PATTERNS.md` lines 626-633.

Write the new `SCOPE.md` sections in English to match the planning corpus.
`forge/documents/` and Forge's source documentation use Chinese prose; copy their
table structure, identifier discipline, and boundary wording, not their language
or Forge-specific claims.

## No Analog Found

| Item | Role | Data Flow | Reason |
|------|------|-----------|--------|
| Eight-row Forge capability grouping | planning document section | transform | No existing document groups Forge capabilities at exactly this granularity; compose from the FR/NFR tables, the architecture component table, and technical-design §10. Source content is D-04 through D-08 |
| Nine-row EROS exclusion table with `Exit criterion` | planning document section | transform | Existing Out of Scope tables have only `Feature / Reason` and no status or exit criterion. Source content is D-09 through D-12 |
| Four-value exclusion status vocabulary | planning document convention | transform | Existing vocabularies are acceptance-execution states or `Pending` / `Complete` traceability values; D-10 defines a new scope status set |
| Combined code-path + authoritative-document evidence cell | planning document convention | transform | Existing analogs cite code paths and documents in separate columns or prose; D-07 requires both in one cell |

## Metadata

**Analog search scope:** `agents/planning/`, `agents/planning/codebase/`,
`agents/planning/phases/01-system-composition-and-portability-boundary/`,
`forge/documents/`, `forge/tests/`, `forge/examples/`, and workspace-root
`AGENTS.md`.

**Files scanned:** 18 — `02-CONTEXT.md`, `SCOPE.md`, `PROJECT.md`,
`REQUIREMENTS.md`, `ROADMAP.md`, `01-PATTERNS.md`, `AGENTS.md` (root and
`agents/`), `ARCHITECTURE.md`, `STRUCTURE.md`, `TESTING.md`, `CONCERNS.md`,
`forge/README.md`, `forge/AGENTS.md`, `forge/documents/requirements.md`,
`forge/documents/test-design.md`, `forge/documents/technical-design.md`,
`forge/documents/README.md`, `forge/tests/README.md`, `forge/examples/README.md`.

**Tracked-source gate:** All analog paths were verified with `git ls-files` in
their owning repository. `agents/planning/*` is tracked by the `agents`
repository; `forge/*` paths are tracked by the `forge` repository. `.planning/`
is a symlink to `agents/planning/`; no gitignored install or runtime mirror was
used.

**Pattern extraction date:** 2026-11-04
