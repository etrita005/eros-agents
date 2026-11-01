# Testing Patterns

**Analysis Date:** 2026-11-02

## Test Framework

**Runner:**
- Python standard-library `unittest`, supplied by the selected Python runtime; no separately pinned pytest/test-runner dependency is detected. See `forge/tests/test_runner_unit.py` and `forge/bazel/packaging/driver_test.py`.
- Custom acceptance runner: `forge/tests/test_runner.py`, with explicit `Case` registration in `forge/tests/acceptance_cases.py`.
- Config: no `pytest.ini`, `tox.ini`, `pyproject.toml`, or framework configuration is detected. Commands and environment policy live in `forge/tests/README.md` and `forge/AGENTS.md`.
- Build-side compiler/tool prerequisites come from the controlled image. Host Python 3.9+ is the launcher requirement, not permission to run all build-side suites with arbitrary host tools (`forge/README.md`).

**Assertion Library:**
- Use `unittest.TestCase` assertions in unit suites, such as `assertEqual`, `assertIn`, `assertRaisesRegex`, and `subTest` (`forge/tests/test_sources_unit.py`, `forge/tests/test_runner_unit.py`).
- Use acceptance `require(condition, message)` from `forge/tests/test_runner.py`; it raises `AssertionError` with a scope-specific diagnostic.

**Run Commands:**

Run from `/home/user/programs/eros-ws/eros/forge`. Commands below are documented entry points, not results of this mapping pass (`forge/AGENTS.md`, `forge/tests/README.md`).

```bash
# Host-only launcher checks; Docker integration remains skipped by default.
python3 -B -m unittest discover -s tests -p test_container_entry.py

# Enter the controlled environment before build-side checks.
./forge-shell
PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover -s tests -p 'test_*_unit.py'
python3 bazel/toolchain/environment_test.py
python3 bazel/packaging/driver_test.py
python3 bazel/toolchain/generate_files.py --check

# Focus one suite inside the controlled environment.
python3 -B -m unittest discover -s tests -p test_runtime_export_unit.py -v

# Enumerate acceptance scopes before selecting them.
python3 tests/test_runner.py --list
python3 tests/test_runner.py --tc TC-SAN-01 --strict --report /tmp/forge-asan.json
```

- Opt-in Docker integration: `FORGE_TEST_DOCKER=1 python3 -B -m unittest discover -s tests -p test_container_entry.py`; this creates a disposable container, so use it deliberately (`forge/tests/test_container_entry.py`).
- Whole registered acceptance suite: `python3 tests/test_runner.py --all --strict --report /tmp/forge-release.json` inside the controlled environment. This is expensive and does not cover every designed release gate (`forge/tests/README.md`).
- Watch mode: not configured in `forge/tests/README.md`; use focused repeat runs rather than assuming a watcher exists.
- Coverage command: not configured in `forge/tests/README.md`; do not present acceptance reports as code-coverage measurements.
- Do not start Docker, download server/debugger dependencies, or run broad acceptance merely to verify documentation changes (`forge/AGENTS.md`).

## Test File Organization

**Location:**
- General regressions are separate from implementation under `forge/tests/`.
- Specialized packaging and environment suites are colocated with their implementation under `forge/bazel/packaging/` and `forge/bazel/toolchain/`.
- Consumer tutorials in `forge/examples/` are not a substitute for fixtures or regression evidence (`forge/AGENTS.md`).

**Naming:**
- Add `test_<area>_unit.py` for discovery by the documented build-side command; keep host-entry checks in `forge/tests/test_container_entry.py`.
- Preserve `*_test.py` colocated suites and invoke them separately: the `test_*_unit.py` discovery pattern does not collect them (`forge/bazel/packaging/driver_test.py`, `forge/bazel/toolchain/environment_test.py`).
- Name test methods `test_<contract_or_failure>` and classes by domain, e.g. `SourceSnapshotTests` in `forge/tests/test_sources_unit.py`.

**Structure:**

```text
forge/
├── tests/
│   ├── test_container_entry.py        # Host launcher + explicit Docker opt-in
│   ├── test_action_unit.py            # Driver boundaries and real compiler checks
│   ├── test_conan_identity_unit.py    # Dependency identity regressions
│   ├── test_packaging_unit.py         # Packaging regressions
│   ├── test_runner_unit.py            # Acceptance runner semantics
│   ├── test_runtime_export_unit.py    # Real ELF + Bash export CLI
│   ├── test_sources_unit.py           # Source archive filtering and safety
│   ├── test_snapshot_sources_unit.py  # Source snapshot regressions
│   ├── test_runner.py                 # CLI, execution context, reports
│   ├── acceptance_cases.py            # Registered acceptance callbacks
│   └── fixtures/                     # Bazel/CMake/C++/Conan test projects
└── bazel/
    ├── packaging/driver_test.py       # Archive, ownership, closure boundaries
    └── toolchain/environment_test.py  # Tool/SDK identity checks
```

## Test Structure

**Suite Organization:**

Use the self-contained temporary-tree pattern from `forge/tests/test_sources_unit.py`:

```python
class SourceSnapshotTests(unittest.TestCase):
    def test_source_link_cannot_escape_to_producer_home(self):
        with tempfile.TemporaryDirectory(prefix="forge-sources-test-") as temporary:
            root = Path(temporary)
            source = root / "source"
            source.mkdir()
            (root / "private.h").write_text("private producer state\n")
            (source / "escape.h").symlink_to("../private.h")
            with self.assertRaisesRegex(ValueError, "escapes"):
                deployer.capture_sources(source, root / "snapshot")

if __name__ == "__main__":
    unittest.main()
```

**Patterns:**
- Arrange fixtures explicitly, invoke one boundary, then assert exact contents, metadata, status, or diagnostics (`forge/tests/test_sources_unit.py`).
- Use `setUp()` plus `self.addCleanup(temporary.cleanup)` when many tests share directory setup (`forge/bazel/packaging/driver_test.py`).
- Use `setUpClass()` and `addClassCleanup()` for costly real compiler fixtures, then copy per test for isolation (`forge/tests/test_runtime_export_unit.py`).
- Use `subTest` for related malformed inputs or configuration combinations, rather than duplicating test bodies (`forge/tests/test_action_unit.py`, `forge/tests/test_runner_unit.py`).
- Assert cleanup and non-effects as well as success: no published archive on failure, no fallback calls, and no source evidence overwrite (`forge/tests/test_runtime_export_unit.py`, `forge/tests/test_runner_unit.py`).

## Mocking

**Framework:** Standard-library `unittest.mock.patch`, `patch.object`, and `patch.dict`; no third-party mock framework is detected (`forge/tests/test_runner_unit.py`).

**Patterns:**

Patch at the module lookup boundary and inspect exact subprocess behavior. Example adapted directly from `forge/tests/test_runner_unit.py`:

```python
with tempfile.TemporaryDirectory(prefix="forge-runner-test-") as temporary:
    context = Context(Path(temporary), "linux_x86_64")
    with patch.object(
        context, "run",
        return_value=subprocess.CompletedProcess([], 0, "Machine: AArch64"),
    ):
        elf(context, Path("target"), "linux_x86_64_cross_arm64")
        with self.assertRaises(AssertionError):
            elf(context, Path("target"))
```

- Patch environment dictionaries with `patch.dict(os.environ, ...)`, and reset `tempfile.tempdir` when checking `TMPDIR` behavior (`forge/tests/test_runner_unit.py`).
- Capture CLI output with `redirect_stdout`/`redirect_stderr` and `io.StringIO`; assert exit status using `assertRaises(SystemExit)` (`forge/tests/test_container_entry.py`).
- Use `side_effect` for argument-dependent fake Docker/subprocess results, and inspect `.call_args`, `.call_args_list`, and `assert_not_called()` to verify isolation (`forge/tests/test_container_entry.py`, `forge/tests/test_runner_unit.py`).
- For direct driver imports, use `importlib.util.spec_from_file_location` and temporarily inject shared modules with `patch.dict(sys.modules, ...)` (`forge/tests/test_action_unit.py`).

**What to Mock:**
- Docker transport, process startup, remote bootstrap, and expensive command execution when asserting wrapper/runner contracts (`forge/tests/test_container_entry.py`, `forge/tests/test_runner_unit.py`).
- Controlled compiler query responses for malformed runtime-export inputs, while keeping actual ELF payloads real (`forge/tests/test_runtime_export_unit.py`).

**What NOT to Mock:**
- Do not replace archive/path/hash logic with mocked success when testing those contracts: use temporary files, actual symlinks, and real archive inspection (`forge/tests/test_sources_unit.py`, `forge/bazel/packaging/driver_test.py`).
- Do not treat mocked parser/CLI tests as proof of compiler, debugger, sanitizer, target runtime, or cross-machine cache behavior. Real acceptance callbacks are separate in `forge/tests/acceptance_cases.py`.

## Fixtures and Factories

**Test Data:**

Use small explicit fixture builders rather than a global factory framework. `forge/bazel/toolchain/environment_test.py` constructs dictionaries with tool identities; `forge/bazel/packaging/driver_test.py` builds minimal ELF bytes:

```python
def elf(machine=183):
    identification = b"\x7fELF\x02\x01\x01" + b"\0" * 9
    header = struct.pack(
        "<HHIQQQIHHHHHH", 1, machine, 1, 0, 0, 0, 0, 64, 56, 0, 64, 0, 0
    )
    return identification + header
```

- Synthetic ELF headers verify parsing/architecture boundaries, not executable correctness (`forge/bazel/packaging/driver_test.py`).
- Real runtime-export fixtures compile AArch64 shared objects, use paths containing spaces, and inspect symlink targets, hashes, archive ownership, timestamps, and deterministic bytes (`forge/tests/test_runtime_export_unit.py`).

**Location:**
- `forge/tests/fixtures/basic/`: equivalent native Bazel, CMake adapter, and Conan/CMake build paths.
- `forge/tests/fixtures/link/`: transitive links, public headers, static/shared/PIC consumers.
- `forge/tests/fixtures/conan/`: locked recipes, controlled remotes, source fallback, real consumer scenarios, and `verify.py`.
- `forge/tests/fixtures/inputs/`: declared-input invalidation; `forge/tests/fixtures/consumer/`: independent consuming workspace.
- `forge/tests/fixtures/runtime/` and `forge/tests/fixtures/cmake_runtime/`: runtime closure, plugins, resources, and publication boundaries.
- `forge/tests/fixtures/debug/`, `forge/tests/fixtures/san/`, `forge/tests/fixtures/fail/`, and `forge/tests/fixtures/checks/`: symbols, sanitizers, and intentional failure cases.
- Preserve fixture intent and register new acceptance callbacks in `forge/tests/acceptance_cases.py`, rather than adding only a runnable example.

## Coverage

**Requirements:** No numerical line/branch threshold or coverage instrumentation is detected. `forge/tests/README.md` and `forge/tests/test_runner.py` enforce scope/evidence completeness rather than a coverage percentage.

**View Coverage:**
- No repository-provided coverage report command is detected in `forge/tests/README.md`.
- Use `python3 tests/test_runner.py --list` inside the controlled environment to inspect registered scopes, not line coverage (`forge/tests/test_runner.py`).
- Unit tests explicitly track unregistered design cases: `TC-ENV-05` through `TC-ENV-07`, `TC-ENTRY-01` through `TC-ENTRY-05`, and `TC-CI-03` in `forge/tests/test_runner_unit.py`. Treat them as unregistered acceptance requirements, not as absence of all related unit checks.
- Registered external scopes may intentionally be BLOCKED; examples include independent-host remote cache, SDK upgrades, and target execution in `forge/tests/acceptance_cases.py`.

## Test Types

**Unit Tests:**
- Host launcher checks isolate path validation, metadata mounts, image/container checks, and environment/state boundaries (`forge/tests/test_container_entry.py`).
- Driver/packaging/source suites cover configuration propagation, identities, archives, declared inputs, path traversal, ownership, and debug artifact boundaries (`forge/tests/test_action_unit.py`, `forge/tests/test_sources_unit.py`, `forge/bazel/packaging/driver_test.py`).
- The `_unit.py` suffix does not guarantee pure host-only execution: some tests invoke GCC/binutils or Bash, and missing prerequisites may produce skips or errors (`forge/tests/test_action_unit.py`, `forge/tests/test_runtime_export_unit.py`).

**Integration Tests:**
- Docker entry integration is explicitly gated by `FORGE_TEST_DOCKER=1` (`forge/tests/test_container_entry.py`).
- Acceptance scopes build actual Bazel targets, inspect artifacts/actions, run compatible native programs, invoke GDB/sanitizers, and inspect Debian payloads (`forge/tests/acceptance_cases.py`).
- HTTP Conan scenarios require deliberate isolated server preparation; do not silently bootstrap network services during unrelated checks (`forge/tests/README.md`, `forge/tests/fixtures/conan/verify.py`).

**E2E Tests:**
- No browser/UI E2E framework is detected. End-to-end infrastructure checks are custom acceptance callbacks in `forge/tests/acceptance_cases.py`.
- Cross compilation and ELF inspection are not target ABI/execution acceptance; extracted Debian layouts are not clean target install/uninstall evidence (`forge/tests/README.md`).

## Common Patterns

**Async Testing:**
- No async/await test harness is detected. Process and concurrency checks use synchronous subprocess APIs and explicit timeouts (`forge/tests/test_runner.py`, `forge/tests/test_runner_unit.py`).
- Acceptance `Context.run()` normalizes timeouts to status 124 and OS launch failures to status 127, records logs, and then checks expected status (`forge/tests/test_runner.py`).
- Test process lifetime cleanup explicitly, e.g. `terminate.assert_called_once_with()` and `wait.assert_called_once_with(timeout=5)` in `forge/tests/test_runner_unit.py`.
- The local concurrent Conan scope checks two real actions and separate private homes; it does not establish independent-host interruption/retry acceptance (`forge/tests/acceptance_cases.py`, `forge/tests/README.md`).

**Error Testing:**

Use exact exception and message matching for validation boundaries (`forge/bazel/toolchain/environment_test.py`):

```python
with self.assertRaisesRegex(ValueError, "manifest digest mismatch"):
    environment.validate(manifest)
```

For expected build failures, use `Context.negative()` from `forge/tests/test_runner.py`; it requires both a nonzero exit and a matching diagnostic, not merely any failure:

```python
result = self.build(target, expected=None, **kwargs)
require(result.returncode != 0, f"Invalid target unexpectedly built: {target}")
require(re.search(pattern, result.stdout, re.IGNORECASE),
        f"Failure for {target} did not identify {pattern!r}:\n{result.stdout}")
```

**Acceptance Reporting:**
- Preserve `PASS`, `FAIL`, `SKIP`, and `BLOCKED`; unavailable prerequisites are never passes (`forge/tests/test_runner.py`).
- A failure yields exit 1; strict incomplete scopes yield exit 2; non-strict runs can return zero despite unavailable scopes (`forge/tests/README.md`).
- Select base IDs to run every registered scope for a case, or full IDs such as `TC-ENV-01/local` for a single scope; unknown IDs fail (`forge/tests/test_runner.py`).
- Keep one new evidence directory outside sources per invocation, numbered command logs, per-case result JSON, source/lock/environment provenance, and checkpoint reports (`forge/tests/test_runner.py`, `forge/tests/README.md`).
- Use the run's explicit Bazel output base and actual execution root, not mutable workspace output symlinks. Shutdown retains artifacts and can consume substantial inodes (`forge/tests/test_runner.py`, `forge/tests/README.md`).
- Do not equate the report's `acceptance_complete` over registered scopes with completion of the entire design specification; pending cases are explicitly tracked in `forge/tests/test_runner_unit.py`.
- This mapping pass inspects source and instructions only; it makes no test-pass or environment-validation claim.

---

*Testing analysis: 2026-11-02*
