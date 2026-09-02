
---
name: project-maintainer
description: A role that works over Git repositories in a Test Driven Development way.
---

# project-maintainer

Instructions for the agent to follow when this skill is activated.

## When to use

Use this skill when you are asked to implement a feature, fix a bug, or perform any code change in a Git repository while strictly following TDD principles and a disciplined commit policy. The skill automatically detects the build/test system, enforces pre‑commit hooks, and guides you through a commit loop that ensures every change is properly tested and documented.

## Instructions

### 0. Repository Setup and Branching

1. **Clone the repository** (if not already present) using the provided URL or local path.
2. **Create a new working branch** with a name that reflects the user’s requirement (e.g., `feature/xxx`, `fix/xxx`, or a short descriptive slug). Use the user’s description to generate the branch name.
3. **Identify the default branch** (usually `main`, `master`, or `trunk`) and note its commit hash.

### 1. Detect Build and Test System

Scan the repository root to determine how the project is built and tested. Look for the following files, in order of precedence:

- `Makefile` – use `make` and `make test` (or check for `test` target)
- `CMakeLists.txt` – use `cmake` and `ctest`
- `pyproject.toml` – check for `[tool.uv]` or `[project]`; prefer `uv` if present, otherwise `pytest`
- `package.json` – use `npm` or `bun`; look for `test` script
- `Cargo.toml` – use `cargo test`
- `Gemfile` – use `bundle` and `rake test` or `rspec`
- `.cabal` or `stack.yaml` – use `cabal test` or `stack test`
- `pom.xml` – use `mvn test`
- `build.gradle` – use `gradle test`
- Any other common patterns (e.g., `setup.py`, `go.mod`).

**Record**:
- The command(s) to compile/build the project (if needed).
- The command(s) to run tests.
- The command to run a single test file or specific test (if discoverable).

### 2. Install Git Pre‑commit Hook for TDD Discipline

If no pre‑commit hook exists, **create one** (at `.git/hooks/pre-commit`) that enforces the following rules. The hook should abort the commit if any rule is violated.

The hook must:

- Prevent commits that touch **both** `src/` and `tests/` directories (or equivalent, e.g., `lib/` and `spec/`). A commit may touch files only from one of these two categories.
- Validate that the commit message follows the type‑based convention (see rules below).
- Enforce the file‑count rule: a commit must touch **at most one file** (excluding binary files, documentation, or configuration files if the project allows exceptions – but for code, it’s strictly one file per commit). If multiple files are staged, the commit should be rejected.
- Allow the `[WIP]` label – see rules below.

**Implement the hook** in a shell script or using a language appropriate for the environment. The hook must parse the staged files and the commit message.

### 3. Commit Message and Type Rules

Define the commit message format as:

```
<type>(optional-scope): short description

[optional body with explanation]
[optional goal: ... for WIP commits]
```

#### Allowed commit types:

**Types that can touch `tests/` (only these three)**:
- `feature-request` – adding a test for a new feature that does not exist yet.
- `architecture-change` – adding tests to cover a planned architectural refactor.
- `bug-reproduction` – adding a test that reproduces an existing bug.

**Types that can touch `src/` (only these four)**:
- `feature-impl` – implementation that makes a previously failing test pass.
- `major-refactor` – significant structural changes without behaviour change.
- `minor-refactor` – small cleanups, renaming, or performance tweaks.
- `bug-fix` – fixing a bug that was previously reproduced by a test.

**Additional constraints**:

- A commit that touches `src/` **must** make all tests pass (or at least the relevant test suite) unless it is a `major-refactor` or `minor-refactor` (these do not require new passing tests, but they must not break existing tests).
- For `feature-impl` commits, the commit message **must** reference the test that was previously failing (e.g., “Implements feature X, passing test Y”).
- A `src` commit that has the **previous commit** (i.e., the immediate parent) also being a `src` commit **must not** be labelled `feature-impl`. This prevents chaining implementations without intervening test‑first steps. Use `major-refactor`, `minor-refactor`, or `bug-fix` instead.

**WIP (Work In Progress) commits**:

- A commit may be prefixed with `[WIP]` (e.g., `[WIP] feature-request: ...`).
- Every `[WIP]` commit must include a `goal:` section in the commit body that states the overall objective of this sequence.
- If the previous commit is also `[WIP]`, the `goal:` must be **exactly identical** to ensure continuity.
- `[WIP]` commits are exempt from the “only one file” rule only if all changes are within the same logical area (but the hook may still enforce single‑file for simplicity; if multiple files are needed, the agent should split them into separate `[WIP]` commits).

### 4. Soft Constraints and Encouraged Practices

- **Commit message explanations**: Whenever possible, include a short explanation of the test‑failure output that motivated the change (especially for `feature-impl` commits). For `minor-refactor` commits, explain what the minor change improves (e.g., readability, performance, maintainability).
- For `feature-impl` commits, the commit message **must** reference the previously failing test(s) and include the actual error output if useful.
- Commits that are not `feature-impl` and do not make new tests pass should still include a rationale.

### 5. The Commit Loop

Once the pre‑commit hook is in place and the build/test commands are known, the agent enters a **commit loop**:

1. **Start with a test commit** – Choose one of the test‑touching types (`feature-request`, `architecture-change`, or `bug-reproduction`) and write a failing test (or a set of tests) in a single file under `tests/`. Commit with a proper message.
2. **Run the tests** to confirm they fail.
3. **Then implement** – Write the minimal code in `src/` to make the test pass. Commit with `feature-impl` (or appropriate type) while referencing the failing test.
4. **Run tests** to confirm they now pass.
5. **Repeat** – alternate between test commits and implementation commits, following the type restrictions and single‑file rule.
6. **Refactoring** – At any point, you may insert a `minor-refactor` or `major-refactor` commit (touching only `src/` or only `tests/` as appropriate) as long as it does not break tests.
7. **Complete** – Continue until the user’s requirement is fully implemented and all tests pass.

**Important**: Each commit must be atomic and self‑contained. If a change spans multiple files, break it into multiple commits (with `[WIP]` if necessary).

### 6. Final Patch and Completion

After the commit loop is finished and all tests pass:

1. **Produce a unified diff** comparing the working branch with the default branch. Use:
   ```bash
   git diff <default-branch>..<working-branch> > patch.diff
   ```
2. **Include in the patch file** a header that indicates:
   - The name of the default branch (e.g., `main`).
   - The name of the working branch.
   - The commit hashes of both branch tips.
3. **Save the patch file** as `patch.diff` in the repository root (or a location specified by the user).
4. **Output the patch** to the user or attach it as requested.

The coding task is then considered complete.

---

## Example Workflow

1. User says: “Add a login feature to the Python project.”
2. Agent clones repo, creates branch `feature/login`.
3. Detects `pyproject.toml` and `pytest`.
4. Installs pre‑commit hook with the TDD rules.
5. Creates a test file `tests/test_login.py` with a failing test for login.
   - Commit: `feature-request: add failing test for login`
6. Runs tests – they fail.
7. Implements login in `src/login.py` to pass the test.
   - Commit: `feature-impl: implement login, passing test_login`
   - Message includes the test failure output.
8. Repeats for additional cases (e.g., invalid credentials).
9. Eventually refactors (e.g., `minor-refactor: rename variable for clarity`).
10. All tests pass; generates `patch.diff` and notifies user.

---

## Notes for the Agent

- Always verify that the pre‑commit hook is executable (`chmod +x .git/hooks/pre-commit`).
- If the project uses a sub‑directory for source and tests (e.g., `lib/` and `spec/`), adapt accordingly.
- If the build system is not recognised, ask the user for the correct commands.
- Ensure you never commit anything that leaves the repository in a broken state (tests must pass after each `src` commit).
- Use `git add -p` or `git add <file>` explicitly to stage only the intended file per commit.

By following these instructions, you will deliver a well‑tested, cleanly‑committed patch that adheres to strict TDD and Git discipline.
