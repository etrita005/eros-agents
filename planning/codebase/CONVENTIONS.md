# Coding Conventions

**Analysis Date:** 2026-11-02

## Scope and Applicable Instructions

- Apply the concrete contributor rules in `forge/AGENTS.md` to Forge changes; the workspace-root `AGENTS.md` is a broken symlink and supplies no readable policy.
- Implementation conventions in this map come from `forge/`; `agents/README.md` and `documents/README.md` are documentation-repository entry points, not application implementations.
- No project skill directories or `SKILL.md` files are detected under `.kilo/` or the source tree. The configured mapper-skills query returns no injected skills.
- Keep requirements/design intent separate from implemented behavior: `forge/tests/acceptance_cases.py` defines runnable scopes, while `forge/documents/test-design.md` describes acceptance requirements beyond those scopes.
- Follow adjacent style rather than introducing a formatting policy during functional changes, as required by `forge/AGENTS.md`.

## Naming Patterns

**Files:**
- Use snake_case Python modules: `forge/forge_container.py`, `forge/bazel/tools/action_common.py`, and `forge/bazel/tools/cmake_action.py`.
- Name regression modules `test_<area>_unit.py` under `forge/tests/`; preserve the distinct host-entry suite `forge/tests/test_container_entry.py`.
- Preserve specialized colocated names: `forge/bazel/packaging/driver_test.py` and `forge/bazel/toolchain/environment_test.py`.
- Use `.bzl` for reusable rule/macro implementation and `BUILD.bazel` for package declarations, as in `forge/bazel/cmake_forge.bzl` and `forge/tests/fixtures/basic/BUILD.bazel`.
- Use `.cc` and `.h` for C++ fixtures, with package-local `CMakeLists.txt`; see `forge/tests/fixtures/basic/main.cc` and `forge/tests/fixtures/link/foo.h`.

**Functions:**
- Use snake_case for Python and Starlark functions: `check_startup_paths`, `validate_tree`, and `cmake_forge` in `forge/forge_container.py`, `forge/bazel/tools/action_common.py`, and `forge/bazel/cmake_forge.bzl`.
- Prefix internal helpers with `_` where the module distinguishes public validation from implementation, e.g. `_link_destination` in `forge/bazel/packaging/driver.py`.
- Name Starlark rule implementations `_<rule>_impl` and keep public macros unprefixed; see `_toolchain_file_impl` and `cmake_forge` in `forge/bazel/cmake_forge.bzl`.
- Name tests for the contract being asserted, including the negative outcome: `test_source_link_cannot_escape_to_producer_home` in `forge/tests/test_sources_unit.py`.

**Variables:**
- Use snake_case locals and uppercase module constants, e.g. `source_roots`, `STARTUP_PATHS`, and `ROOT` in `forge/forge_container.py`.
- Keep serialized keys snake_case and explicit about identity or units: `schema_version`, `environment_sha256`, and `duration_seconds` in `forge/tests/test_runner.py` and `forge/bazel/tools/action_common.py`.
- Preserve standard CMake variable names and Bazel labels rather than translating them into Python naming: `CMAKE_TOOLCHAIN_FILE` and `//bazel/config:debug` in `forge/bazel/cmake_forge.bzl`.

**Types:**
- Use PascalCase for classes and exceptions: `Case`, `Result`, `Context`, `Blocked`, and `Skipped` in `forge/tests/test_runner.py`.
- Use descriptive provider names ending in `Info`: `ErosInstallInfo` and `ErosDebugInfo` in `forge/bazel/providers.bzl`.
- Use frozen dataclasses for value records and immutable ownership sets, as with `Entry` in `forge/bazel/packaging/driver.py`; mutable execution/report state remains in `Context` and `Result` in `forge/tests/test_runner.py`.

## Code Style

**Formatting:**
- No repository formatter configuration is detected in `forge/` or the workspace root: no `pyproject.toml`, `setup.cfg`, `.editorconfig`, or `.clang-format` is found. Do not claim Black, Ruff, or clang-format enforcement.
- Match four-space Python/Starlark indentation, double-quoted ordinary strings, and blank lines between top-level Python definitions in `forge/bazel/tools/action_common.py` and `forge/bazel/cmake_forge.bzl`.
- Preserve local wrapping; long messages and compact dictionaries exist in `forge/forge_container.py` and `forge/tests/test_runner_unit.py`. No fixed line-length policy is evident.
- Use spaced Starlark keyword arguments (`name = ...`) and trailing commas in multiline declarations, as in `forge/tests/fixtures/basic/BUILD.bazel`; Python calls use `name=...`.
- C++ fixtures use two-space indentation and same-line opening braces in `forge/tests/fixtures/basic/main.cc`. Treat this as a fixture convention, not evidence of a broad robot-runtime style guide.

**Linting:**
- No configured Python linter, type-checker, pre-commit configuration, or JavaScript lint tool is detected. `forge/AGENTS.md` specifies regression and generator checks instead.
- Preserve generated-file consistency using `forge/bazel/toolchain/generate_files.py --check`; this is a generation invariant, not a formatting/lint command.
- Keep host launcher code compatible with Python 3.9+ (`forge/README.md`). Build-side files use modern unions such as `Path | None` in `forge/bazel/packaging/driver.py`; do not copy these blindly into host-only entry code.

## Import Organization

**Order:**
1. Module docstring and optional future import, as in `forge/tests/test_runner.py`.
2. Standard-library imports, generally grouped in roughly alphabetical order; `from pathlib import Path` appears among these imports in `forge/forge_container.py`.
3. Local implementation/test imports after a separating blank line, as in `forge/tests/test_runner_unit.py`.

**Path Aliases:**
- Python package aliases are not configured. Scripts use direct sibling imports, explicit `sys.path` insertion, or `importlib.util.spec_from_file_location`; see `forge/tests/test_container_entry.py` and `forge/tests/test_action_unit.py`.
- Follow the existing standalone-module loading pattern when testing execution drivers without turning them into a new package:

```python
# Pattern from forge/tests/test_sources_unit.py
SPEC = importlib.util.spec_from_file_location(
    "forge_conan_deployer", ROOT / "bazel/tools/conan_deployer.py"
)
deployer = importlib.util.module_from_spec(SPEC)
SPEC.loader.exec_module(deployer)
```

- In Starlark, load external dependencies with `@repository//...`, project packages with `//bazel/...`, and siblings with `:file.bzl`; examples appear together in `forge/bazel/cmake_forge.bzl`.
- Do not impose strict import sorting: `forge/bazel/tools/BUILD.bazel` and `forge/bazel/cmake_forge.bzl` use purposeful local grouping rather than an enforced sorter.

## Error Handling

**Patterns:**
- Fail closed on invalid paths, missing outputs, identity mismatches, and escaping links. Use the shared validators in `forge/bazel/tools/action_common.py` and `forge/bazel/packaging/driver.py` rather than silently repairing inputs.
- Raise `ValueError` for invalid action inputs, `PackagingError(ValueError)` for packaging contract violations, and `RuntimeError` for host Docker transport failures; see `forge/bazel/tools/action_common.py`, `forge/bazel/packaging/driver.py`, and `forge/forge_container.py`.
- Include the offending path or contract in diagnostics, without logging secrets. Example from `forge/bazel/packaging/driver.py`:

```python
def relative_path(value, *, allow_empty=False):
    value = str(value)
    if not value and allow_empty:
        return ""
    if not value or value.startswith("/") or "\\" in value:
        raise PackagingError(f"invalid private installation path: {value!r}")
    parts = value.split("/")
    if any(part in ("", ".", "..") for part in parts):
        raise PackagingError(f"installation path escapes or is not normalized: {value!r}")
    return value
```

- Use `subprocess.run(..., check=True)` for execution drivers where failure should abort, as in `forge/bazel/tools/action_common.py`. The acceptance runner deliberately uses `check=False` to record and assert expected statuses in `forge/tests/test_runner.py`.
- Keep missing prerequisites distinct from product failures: use `Blocked` or `Skipped` in acceptance checks and `unittest.SkipTest`/`self.skipTest` in prerequisite-dependent unit tests (`forge/tests/test_runner.py`, `forge/tests/test_runtime_export_unit.py`).

## Logging

**Framework:** Standard output/error and explicit files; no logging service or shared logging framework is evident in `forge/bazel/tools/action_common.py` or `forge/tests/test_runner.py`.

**Patterns:**
- Execution drivers print `Forge: <command>` with flushing before invoking a tool (`forge/bazel/tools/action_common.py`). Pass argv lists, not shell-concatenated command strings.
- Acceptance commands record argv, cwd, exit code, duration, and combined output in numbered logs; results and failure tracebacks are persisted separately (`forge/tests/test_runner.py`).
- Do not dump complete environment dictionaries or pass secrets as logged command arguments. `forge/tests/test_runner.py` records explicit environment overrides, and `forge/AGENTS.md` prohibits secrets in manifests/logs.

## Comments

**When to Comment:**
- Explain boundary exceptions and tool-specific behavior rather than narrating syntax: the rules_foreign_cc argument-flattening comment in `forge/bazel/cmake_forge.bzl` explains why the overlay exists.
- Document exceptional transport links before relaxing normal path rules, as in `forge/bazel/tools/action_common.py`'s Bazel TreeArtifact copy handling.
- State what evidence does not establish, following the acceptance scope descriptions in `forge/tests/acceptance_cases.py`.

**JSDoc/TSDoc:**
- Not applicable; the inspected implementation is Python, Starlark, shell, CMake, and C++ fixtures, not TypeScript.
- Use module/function docstrings for contracts and provider `doc`/`fields` text for analysis-time interfaces, following `forge/bazel/packaging/driver.py` and `forge/bazel/providers.bzl`.

## Function Design

**Size:**
- No size limit is configured. Small validation helpers coexist with substantial orchestration functions in `forge/forge_container.py` and `forge/bazel/packaging/driver.py`. Prefer extending focused helpers where possible without claiming a repository-wide short-function rule.

**Parameters:**
- Pass `Path` objects and explicit configuration/environment dictionaries rather than discovering implicit host tools; see `load_environment`, `target_tools`, and `run` in `forge/bazel/tools/action_common.py`.
- Use keyword-only behavior controls in typed runner APIs, e.g. `Context.run(command, *, cwd=None, env=None, expected=0, timeout=600)` in `forge/tests/test_runner.py`.
- Preserve the adjacent annotation level: `forge/tests/test_runner.py` is typed, while `forge/forge_container.py` and much of `forge/bazel/tools/action_common.py` are not fully annotated.

**Return Values:**
- Return explicit records/collections for artifacts and provenance, using stable keys and deterministic ordering. `inventory()` sorts paths, and `write_json()` sorts keys in `forge/bazel/tools/action_common.py`.
- Return `CompletedProcess` when callers need status/output inspection (`forge/tests/test_runner.py`); shared action `run()` returns captured stdout or `None` (`forge/bazel/tools/action_common.py`). Preserve this distinction.

## Module Design

**Exports:**
- Keep analysis and rule interfaces in `forge/bazel/*.bzl`; keep execution-time drivers in `forge/bazel/tools/` and `forge/bazel/packaging/`, per `forge/AGENTS.md`.
- Reuse provider boundaries in `forge/bazel/providers.bzl` instead of duplicating private artifact/ownership conventions across rules.
- Keep Bazel responsible for inter-package orchestration, CMake for package-internal builds, and Conan for declared dependencies (`forge/AGENTS.md`).
- Declare public source exports explicitly, as with `exports_files(glob(["*.py"]))` in `forge/bazel/tools/BUILD.bazel`.

**Barrel Files:**
- No Python barrel/package re-export convention is detected. `forge/bazel/providers.bzl` is a shared provider contract, not a Python package facade.

**Shell and generated artifacts:**
- Use strict shell mode, quoted paths, explicit tool checks, and task-owned cleanup traps as in `forge/bazel/toolchain/export_runtime.sh`.
- Preserve hermetic inputs, manifest identities, variant isolation, deterministic serialization, and relative link checks (`forge/bazel/tools/action_common.py`, `forge/bazel/packaging/driver.py`).
- Edit generators/inputs, not `forge/bazel-bin/`, `forge/bazel-out/`, `forge/bazel-testlogs/`, or `forge/bazel-forge/`; `forge/AGENTS.md` explicitly excludes these generated trees from source edits.

---

*Convention analysis: 2026-11-02*
