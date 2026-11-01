# Codebase Concerns

**Analysis Date:** 2026-11-02

## Tech Debt

**Acceptance registry does not cover the complete release contract:**
- Issue: The documented environment/entry/CI cases are not all executable registry entries. Several registered external scopes are unconditional `blocked_case(...)` placeholders rather than environment adapters.
- Files: `forge/tests/acceptance_cases.py:959`, `forge/tests/test_runner.py`, `forge/documents/test-design.md:5`, `forge/documents/technical-design.md:320`.
- Impact: Neither a successful local build nor `--all --strict` establishes complete release acceptance. Merely supplying a target or cache service cannot execute a placeholder with no check implementation.
- Fix approach: Implement external execution/evidence adapters, register TC-ENV-05..07, TC-ENTRY-01..05 and TC-CI-03, and add a registry-versus-design coverage check. Preserve PASS/FAIL/SKIP/BLOCKED distinctions and report completeness checks.
- Evidence boundary: This is an explicit implementation/coverage gap, not a claim that existing local assertions fail.

**Large packaging module combines multiple correctness boundaries:**
- Issue: A 1,386-line module owns path normalization, ownership, symlink resolution, ELF parsing, dependency closure, archive serialization, Debian metadata, runtime publication and symbol pairing.
- Files: `forge/bazel/packaging/driver.py`, `forge/bazel/packaging/driver_test.py`, `forge/tests/test_packaging_unit.py`.
- Impact: A change to common entry collection can affect runtime tarballs, runtime debs and dbgsym packages simultaneously; duplicated traversals and sidecar invariants are difficult to review independently.
- Fix approach: Extract independently tested entry/inventory, ELF/closure and serialization components while retaining the public driver interface. Preserve existing byte-determinism and ownership regressions during refactoring.

**Root onboarding/instruction discovery is incomplete:**
- Issue: Root `AGENTS.md` is a broken symlink as identified in the task context; the root document repositories have only title-level READMEs. Detailed contributor guidance exists under Forge instead.
- Files: `AGENTS.md`, `agents/README.md`, `documents/README.md`, `forge/AGENTS.md`, `forge/README.md`.
- Impact: Repository-root tools cannot rely on root instructions resolving, and new contributors can miss the controlled-toolchain and multi-repository boundaries.
- Fix approach: Repair the instruction target intentionally and provide root navigation to the actual Forge contracts. Do not copy a potentially huge instruction bundle into the codebase map.
- Inspection boundary: No project skills are present under `.kilo/`; the configured mapper skill query returns no entries. Apply the concrete constraints in `forge/AGENTS.md`, particularly controlled builds and no generated-output edits.

## Known Bugs

**Nullable Docker security options produce an uncaught exception:**
- Symptoms: The existing-container guard evaluates membership in `None` before its intended fallback, producing `TypeError` rather than the actionable container-recreation diagnostic.
- Files: `forge/forge_container.py:187`, `forge/forge_container.py:240`, `forge/tests/test_container_entry.py:79`.
- Trigger: An otherwise matching inspected container has `HostConfig.SecurityOpt` set to JSON null. Earlier `or` terms must be false for evaluation to reach this branch.
- Evidence: The exact expression `"no-new-privileges" not in host["SecurityOpt"] or []` with `host = {"SecurityOpt": None}` reproduces `TypeError: argument of type 'NoneType' is not iterable` in a standalone Python check. The outer handler does not catch `TypeError`. No Docker integration reproduction is claimed.
- Impact: Fail-closed rejection remains intact; this is a robustness/diagnostic bug, not evidence of a security bypass.
- Workaround: Inspect and explicitly recreate only the mismatched task-owned container, retaining its host state, as prescribed in `forge/README.md:143`.
- Fix approach: Normalize nullable inspect fields before validation and test null, empty and missing security-option values, including the exact error message.

```python
# Suggested normalization for forge/forge_container.py, not an applied change:
security_options = host.get("SecurityOpt") or []
if "no-new-privileges" not in security_options:
    raise RuntimeError("Existing Forge container has unexpected security settings")
```

## Security Considerations

**Build containers are a controlled environment, not an untrusted-code boundary:**
- Risk: CMake projects and Conan recipes execute code with the caller's UID, writable source/state mounts and host networking. Untrusted recipes can modify mounted source or reach host-network services within that user's privileges.
- Files: `forge/forge_container.py:140`, `forge/forge_container.py:164`, `forge/bazel/tools/conan_action.py:183`, `forge/bazel/tools/cmake_action.py:91`, `forge/README.md:43`.
- Current mitigation: The launcher pins the image, validates environment/mounts, drops capabilities, enables no-new-privileges and binds external Git metadata read-only; action drivers use private homes and explicit input identities.
- Recommendations: Treat project/recipe inputs as trusted executable code. Use dedicated workers and restricted network access for untrusted contributions; do not describe Docker plus hashes as a malicious-code sandbox. Retain Bazel sandboxing inside the container.
- Evidence boundary: These are concrete trust boundaries; no exploit or malicious recipe is observed.

**Command/evidence logging lacks a shared redaction contract:**
- Risk: `action_common.run()` prints complete commands, while the runner retains argv, explicit environment overrides and subprocess output. A remote URL containing user information or a caller-supplied sensitive argument can therefore enter retained logs.
- Files: `forge/bazel/tools/action_common.py:43`, `forge/bazel/tools/conan_action.py:159`, `forge/bazel/tools/conan_lock_update.py:39`, `forge/tests/test_runner.py:129`.
- Current mitigation: The Protobuf checker redacts configured proxy values in command displays and captured output; the README prohibits committing proxy credentials. Other execution helpers do not share that mechanism.
- Reference pattern: `forge/examples/04b-cmake-protobuf/check.py:101` defines `redact()` and applies it to argv and stdout/stderr.
- Recommendations: Reject URL user-info in declared remotes, use a separate approved authentication channel, and centralize argument/environment/output redaction before persistence. Add synthetic-marker tests rather than using real credentials.
- Evidence boundary: Conditional leakage risk from inspected code, not a finding of exposed credentials. Secret files and values are outside this audit.

**HTTP remote acceptance needs an explicit production policy:**
- Risk: Binary remote configuration accepts both HTTP and HTTPS without restricting HTTP to loopback fixtures.
- Files: `forge/bazel/tools/conan_action.py:159`, `forge/tests/README.md:60`.
- Current mitigation: Schema-v2 pins bind the effective variant and every package file/symlink; unpinned precompiled packages and mismatched hashes are rejected before consumption. Loopback HTTP is intentionally used by the isolated test server.
- Recommendations: Require HTTPS for non-loopback production remotes and preserve explicitly scoped local fixture access. Do not remove pin validation or claim HTTP automatically permits a pinned binary substitution.
- Evidence boundary: Transport-policy risk; no demonstrated integrity bypass.

**Local image identity is not reproducible image acquisition:**
- Risk: A locked local image ID is verified at launch, but rebuilding depends on unversioned APT selections and mutable upstream availability. A clean machine cannot assume it can reconstruct the identical image from the Dockerfile.
- Files: `forge/Dockerfile.amd64:5`, `forge/docker/image.lock.json`, `forge/README.md:73`, `forge/README.md:89`.
- Current mitigation: The base image is digest-pinned, pip uses hash-locked dependencies, the Bazel download is SHA256-checked and mismatched local image IDs fail closed.
- Recommendations: Publish a reviewed immutable image artifact or archive and record package provenance/snapshots for intentional upgrades. Establish vulnerability review and rebuild procedures without weakening image validation.
- Evidence boundary: Reproducibility/distribution risk; no specific vulnerable package or CVE is asserted.

## Performance Bottlenecks

**Archive assembly buffers entire payloads in memory:**
- Problem: `_tar_bytes()` constructs an uncompressed tar in `BytesIO`; `_compress()` then constructs compressed bytes, and `_assemble_deb()` concatenates ar members into another complete byte string.
- Files: `forge/bazel/packaging/driver.py:536`, `forge/bazel/packaging/driver.py:576`, `forge/bazel/packaging/driver.py:1098`.
- Cause: Serialization interfaces return complete byte strings instead of writing streams; intermediate uncompressed/compressed buffers can coexist.
- Impact: Memory usage grows with total payload size, not only with the largest input file. Large symbol/source payloads can exhaust worker memory; no measured failure threshold is available.
- Improvement path: Stream tar/gzip output into temporary files and write ar headers plus file content incrementally. Retain sorted entries, zero timestamps, fixed ownership and deterministic gzip headers; assert byte-identical outputs in packaging tests.

**Conan verification and final export repeatedly traverse/hash package trees:**
- Problem: Cache verification, pin verification, pre/post-consumption verification, copying and final inventories revisit the same package bytes multiple times.
- Files: `forge/bazel/tools/conan_action.py:72`, `forge/bazel/tools/conan_action.py:165`, `forge/bazel/tools/conan_action.py:180`, `forge/bazel/tools/conan_action.py:237`, `forge/bazel/tools/action_common.py:80`, `forge/bazel/tools/cmake_action.py:110`.
- Cause: Immutable-input verification is deliberately repeated across trust/lifetime boundaries; complete host package trees are also copied under the install tree.
- Impact: Large dependency graphs incur substantial sequential I/O and metadata work even when binary downloads hit. Cost is inferred from control flow, not benchmarked.
- Improvement path: Profile traversal/hashing separately from compilation. Reuse inventories only within a boundary whose immutability is established; never remove the post-consumption mutation check or substitute timestamps for verified pin content.

**Docker orchestration and action execution have uneven timeout handling:**
- Problem: The common Docker helper and action subprocess helper have no explicit timeouts; the acceptance runner has bounded commands, but these lower-level helpers can wait on an unresponsive daemon or child command.
- Files: `forge/forge_container.py:22`, `forge/forge_container.py:194`, `forge/bazel/tools/action_common.py:43`, `forge/tests/test_runner.py:99`, `forge/bazel/toolchain/environment.py:38`.
- Cause: Time budgeting is local to individual callers rather than a shared policy.
- Improvement path: Bound Docker inspection/verification operations and expose configurable build/download budgets with cancellation cleanup. Distinguish intentional interactive shell lifetime from a hung orchestration call.
- Evidence boundary: Potential hang paths, not an observed daemon hang during this mapping.

## Fragile Areas

**Signal forwarding crosses host/client/container process lifetimes:**
- Files: `forge/forge_container.py:209`, `forge/docker/exec_command.py:12`, `forge/tests/test_container_entry.py:203`.
- Why fragile: A host poll loop waits up to ten seconds for a shared PID file; a signal handler performs blocking Docker exec calls and forwards to one PID. The PID file is written before `execv()`. Startup, command exit and signal delivery can race.
- Safe modification: Preserve session-local cancellation and test delayed PID publication, failed exec, exit-before-signal, repeated signals and unrelated concurrent sessions. Do not switch to killing the whole persistent container.
- Test coverage: There is an opt-in Docker signal test, but it does not establish every startup/reentrancy race. Potential lost/delayed cancellation or PID-reuse effects require targeted reproduction; they are not confirmed bugs here.

**Bazel transport symlinks are distinguished by path-shape heuristics:**
- Files: `forge/bazel/tools/action_common.py:95`, `forge/bazel/packaging/driver.py:129`, `forge/bazel/packaging/driver_test.py`.
- Why fragile: `copy_tree()` recognizes `/bazel-out/` suffixes; packaging detects a `sandbox` path component and matching artifact suffixes to peel transport indirection. Bazel layout changes can invalidate these assumptions.
- Safe modification: Keep transport dereferencing separate from payload symlink validation; add fixtures for local sandbox, external-module and materialized/cache output layouts. Never generally permit arbitrary absolute payload links to fix a transport case.
- Test coverage: Packaging has dedicated regression tests, but independent-host remote-cache behavior remains blocked in `forge/tests/acceptance_cases.py:982`.

**Conan cache lifetime and CMake consumption must remain one action:**
- Files: `forge/bazel/tools/conan_action.py:113`, `forge/bazel/tools/cmake_action.py:19`, `forge/bazel/tools/cmake_action.py:191`, `forge/tests/README.md:95`.
- Why fragile: Generated CMake metadata points into private Conan package directories. Splitting generation and consumption or deleting the private home too early leaves stale paths; copying a transformed dependency snapshot is not equivalent to consuming verified original bytes.
- Safe modification: Preserve prepare/build/install/export within one action lifetime and verify private-home removal only afterward. Run the single-action, relocation, build-requirement and concurrent-consumer scopes when changing this boundary.
- Test coverage: Local TC-CACHE-01/04/05 scopes cover important invariants; interrupted independent-workspace retry remains a distinct blocked scope.

**Variant identity is shared across Starlark, generated profiles and Python:**
- Files: `forge/bazel/config/variants.bzl`, `forge/bazel/toolchain/generate_files.py`, `forge/bazel/tools/conan_action.py:12`, `forge/bazel/tools/cmake_action.py:47`, `forge/bazel/bazelrc`.
- Why fragile: Compiler flags, sanitizer state, build/host contexts, environment identity and debug policy must agree across generated and hand-written code. The combined ASan/UBSan path also rewrites profile flags.
- Safe modification: Modify generator inputs/generator rather than derived toolchain files; run `python3 bazel/toolchain/generate_files.py --check` inside the controlled environment. Extend variant/pin and quoted-flag tests for each new setting.
- Test coverage: `forge/tests/test_conan_identity_unit.py`, `forge/tests/test_action_unit.py` and registered sanitizer/configuration cases provide regressions, but a legitimate SDK-upgrade/cache-invalidation gate is blocked.

## Scaling Limits

**One active container instance per source workspace:**
- Current capacity: The launcher allows one running instance name per workspace; repeated sessions in that same instance are distinct from independent instances.
- Files: `forge/forge_container.py:148`, `forge/forge_container.py:155`, `forge/README.md:152`.
- Limit: A second `FORGE_INSTANCE` for the same checkout is rejected intentionally to avoid shared output-link conflicts.
- Scaling path: Use separate checkouts and isolated state for parallel jobs. Do not remove the guard without redesigning workspace-output ownership and concurrent acceptance evidence.

**Retained evidence/output bases consume bytes and inodes:**
- Current capacity: No numeric storage bound is documented; each runner invocation allocates independent evidence and an output base, with a bounded JVM but retained files after shutdown.
- Files: `forge/tests/test_runner.py:69`, `forge/tests/README.md:161`, `forge/README.md:140`.
- Limit: File-by-file tool inputs, repositories and sandboxes can exhaust inodes before disk bytes. Persistent container removal does not reclaim host state.
- Scaling path: Establish retention limits and explicit task-owned cleanup after archiving required reports; monitor both free bytes and inodes. Do not prune arbitrary shared caches or treat higher `--jobs` as a capacity fix.

## Dependencies at Risk

**Build tool API/layout coupling:**
- Risk: Bazel sandbox paths/experimental execution-configuration flags and Conan JSON graph/cache schemas are consumed directly.
- Files: `forge/bazel/bazelrc:7`, `forge/bazel/tools/conan_action.py:64`, `forge/bazel/tools/conan_action.py:93`, `forge/bazel/packaging/driver.py:136`, `forge/MODULE.bazel`.
- Impact: A tool upgrade can break dependency identity selection, transport-link handling or host/target separation even if simple builds still succeed.
- Migration plan: Upgrade through an explicit candidate image/lock review, run schema/identity unit tests and external-consumer/build-context/relocation acceptance, and compare action graphs. No end-of-life or known-vulnerability claim is made for the pinned dependencies.

**Local image and upstream package availability:**
- Risk: The supported baseline is a local image rather than a published multi-architecture distribution, and image rebuilds depend on upstream APT/pip/Bazel access.
- Files: `forge/README.md:89`, `forge/Dockerfile.amd64`, `forge/docker/requirements.lock`.
- Impact: A new developer or CI worker can be blocked despite a complete source checkout.
- Migration plan: Distribute the reviewed image by immutable identity and retain required package/source artifacts; keep all content and environment validation active.

## Missing Critical Features

**Matching target SDK/runtime acceptance and external execution adapters:**
- Problem: Cross build/ELF inspection is available without matching target SDK/runtime proof. Target install/run, core/symbol round-trip and independent-machine remote cache cases remain blocked placeholders.
- Files: `forge/README.md:157`, `forge/tests/acceptance_cases.py:965`, `forge/tests/acceptance_cases.py:982`, `forge/tests/acceptance_cases.py:1006`.
- Blocks: Target ABI/deployment confidence, clean dpkg install/uninstall evidence, cross-machine cache claims and complete CI release evidence. Provisioning environments alone does not implement missing runner adapters.

**Validated native ARM64 entry:**
- Problem: ARM64 toolchain/preset definitions and a candidate Dockerfile do not provide a validated launcher baseline; the launcher explicitly accepts only `linux/amd64` in the image lock.
- Files: `forge/Dockerfile.arm64`, `forge/forge_container.py:117`, `forge/README.md:33`.
- Blocks: Supported native ARM64 build-host onboarding. Add a reviewed image/platform baseline and native validation before extending support claims.

## Test Coverage Gaps

**Release-gate completeness:**
- What's not tested: Full environment/entry/CI design coverage in the registered runner and required external target/cache scopes.
- Files: `forge/documents/test-design.md:5`, `forge/tests/acceptance_cases.py:959`, `forge/tests/README.md:31`.
- Risk: A selected or non-strict passing report can be misread as complete acceptance.
- Priority: High. Enforce scope inventory, strict result semantics and external evidence integration.

**Malformed container metadata and signal edge cases:**
- What's not tested: The inspected launcher suite does not cover nullable `SecurityOpt`; its Docker signal test is opt-in and does not cover all PID publication/exit races.
- Files: `forge/tests/test_container_entry.py:79`, `forge/tests/test_container_entry.py:203`, `forge/forge_container.py:187`.
- Risk: Unexpected diagnostics and unreliable cancellation during adverse startup/exit conditions.
- Priority: High for null-field regression; medium for additional timing/stress scenarios.

**Large-payload memory and sustained evidence retention:**
- What's not tested: No capacity benchmark or memory bound is established by the inspected packaging and runner suites.
- Files: `forge/bazel/packaging/driver_test.py`, `forge/tests/test_packaging_unit.py`, `forge/tests/README.md:167`.
- Risk: Small fixture success does not predict archive assembly memory or long-lived worker inode consumption.
- Priority: Medium. Add opt-in large-payload peak-memory tests and retained-output-base capacity checks without making routine unit tests expensive.

**Audit execution boundary:**
- This document is a source/documentation inspection of the repository, centered on implementation under `forge/`; `agents/README.md` and `documents/README.md` contain no application implementation. Generated Bazel trees and secret files are excluded.
- Only the isolated Python security-option expression is executed. No Docker integration, Bazel build, target deployment, acceptance suite, vulnerability scan or performance benchmark is claimed. Use `forge/AGENTS.md` and `forge/tests/README.md` for controlled verification commands and prerequisite rules.

---

*Concerns audit: 2026-11-02*
