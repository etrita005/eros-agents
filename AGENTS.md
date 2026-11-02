# EROS Project Instructions

## Project Identity and Scope

EROS is a robot operating system project managed as multiple independent Git
repositories with `repo`. The workspace root is not a single repository owning
all project source code.

Do not treat the functionality or implementation maturity of one subproject as
the scope or roadmap of EROS as a whole. In particular, Forge is build and
delivery infrastructure, not the entire robot operating system.

## Repository Responsibilities

- `forge/`: Build and delivery infrastructure. Provides a controlled container
  environment, Bazel build orchestration, package-internal CMake builds, Conan
  dependency management, runtime artifact packaging, Debian package generation,
  and build acceptance tooling.
- `documents/`: Project-level documentation repository. Consult its existing
  documentation before adding or duplicating project-wide material.
- `agents/`: Shared agent instructions and project-level planning artifacts.

Use the workspace manifest to confirm repository membership and branch selection.
Do not assume future components already exist or that planned capabilities have
been implemented.

## Repository and Commit Boundaries

- Before editing, staging, committing, or pushing, identify the Git repository
  that actually owns each file. Resolve symbolic links when determining ownership.
- Inspect status and diffs in each affected repository. Preserve unrelated user
  changes and commit each repository independently.
- Do not bulk-stage child repositories from the workspace root or accidentally
  record them as embedded repositories or submodules.
- Check each repository's branch, remote, and detached-HEAD state before Git
  operations. Follow the user's requested target branch; do not assume all
  repositories share the same checkout state.
- Commit and push only when requested. Do not invent a remote for a repository
  that has none.

## Instructions and Planning Ownership

- The workspace-root `AGENTS.md` links to `agents/AGENTS.md`. Maintain this shared
  file rather than a duplicate root-level copy.
- The workspace-root `.planning/` links to `agents/planning/`. Project-level
  planning files belong to the `agents` repository.
- Distinguish EROS-wide goals and cross-repository coordination from individual
  subproject goals. Do not turn Forge's current features into the whole EROS
  roadmap or imply that future system components are already delivered.
- Read applicable subproject instructions before changing that subproject.
  Keep detailed implementation and verification rules close to the owning code.

## Evidence and Acceptance Boundaries

- Distinguish intended requirements, implemented behavior, and verified results.
  Design documents alone are not implementation or acceptance evidence.
- A successful local build or cross-compilation does not establish deployment,
  target ABI compatibility, or acceptance on a robot system.
- Subproject verification does not automatically establish EROS system-level
  acceptance. State the actual environment, target, checks, and limitations.
- Keep passed, failed, skipped, blocked, and unverified results distinct. Do not
  present missing prerequisites or unexecuted checks as successful validation.

## Documentation Navigation

For Forge work, read `forge/AGENTS.md` and the relevant authoritative documents:

- `forge/README.md`: Supported environment, launchers, and consumer setup.
- `forge/documents/README.md`: Documentation index.
- `forge/documents/requirements.md`: Forge requirements and scope.
- `forge/documents/technical-design.md`: Architecture and mechanism contracts.
- `forge/documents/test-design.md`: Acceptance criteria and evidence boundaries.
- `forge/tests/README.md`: Runnable checks, prerequisites, and result semantics.
- `forge/examples/README.md`: Consumer examples.

Keep this file focused on project identity and cross-repository rules. Maintain
Forge-specific build commands, generated-file rules, and test procedures in
`forge/AGENTS.md` rather than duplicating them here.
