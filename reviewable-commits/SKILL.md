---
name: reviewable-commits
description: >
  Use when planning, creating, splitting, staging, or reviewing Git commits for
  the current task, including requests to organize changes or clean up commit
  history. Define small, independently reviewable changes, order them by
  dependency, and preserve review history. Distinguish review and planning
  from permission to modify the index or create commits.
---

# Reviewable Commits

Organize task changes into the smallest coherent commits that a human can review independently.
Keep each commit valid relative to its parent. Preserve existing review history.

## 1. Determine the mode and scope

Select the mode from the user's request:

| Mode | Actions | Completion criterion |
| --- | --- | --- |
| Review | Inspect changes or commits. Report problems and recommendations. | Findings identify affected changes and explain their consequences. |
| Plan | Inspect changes. Propose commit boundaries and dependency order. | Each intended change belongs to one planned commit. |
| Execute | Stage changes or create commits within the user's authorization. | Authorized actions are complete, checked, and reported. |

Loading this skill does not authorize repository changes.
In review and plan modes, leave files, the index, and history unchanged.
A staging-only request does not authorize commits. A commit request does not authorize pushing or rewriting history.
If authorization is unclear, provide a plan and ask before making changes.

Determine which task changes or existing commit range the request covers.
Use the conversation to distinguish prerequisites, requested behavior, corrections, and abandoned experiments.
Use repository evidence to confirm what remains in the files and commits.
Leave unrelated user changes untouched. Ask about ambiguous changes before including them.

## 2. Inspect the repository

Read applicable repository instructions. Inspect the current state:

```bash
git status --short
git diff --stat
git diff
git diff --cached
git log --oneline -12
```

Read relevant untracked files separately. Ordinary `git diff` does not show their contents.
Inspect affected code, tests, and configuration as needed to understand dependencies.
For an existing commit sequence, inspect the requested range and its parent state.
Recent history is context, not a substitute for the full requested range.

Record the starting HEAD and existing staged changes before execution.
Treat existing staged changes as user state, not automatic permission to commit them.
If unrelated staged changes prevent a scoped commit, ask before altering their staging.

Follow explicit commit-message instructions first. Otherwise, infer the convention from repository configuration and recent history.

## 3. Define commit boundaries

Partition the changes by purpose, not by file, directory, architectural layer, or commit-message type.
Use the boundary rules below. Order prerequisite commits before their dependents.

For each candidate commit, check:

- Its purpose fits in one sentence.
- Its diff makes sense relative to its parent.
- It contains the implementation, contracts, and tests needed for that purpose.
- It does not require a future commit to compile or satisfy relevant checks.
- It excludes unrelated changes and abandoned experiments.

Every intended change must belong to exactly one planned commit.
Record changes that will remain uncommitted separately.

### Logical boundaries

Prefer separate commits when each change has an independent purpose and remains valid after separation.
Combine changes when separation creates incomplete, misleading, or broken intermediate states.
Commit count is a consequence of these boundaries, not a target.

Use this review test:

> Can a reviewer evaluate this change against its parent without assuming a future fix?

Use this bisect test:

> If this commit introduces a regression, does its diff identify a coherent change to investigate?

A single file can contain several logical changes. Use selective staging when necessary.
Keep adjacent changes together when separating them would break either commit.

### Prerequisites and contracts

Separate a prerequisite when it has a clear purpose and leaves the repository valid without its dependents.
Examples include a behavior-preserving refactor or reusable test infrastructure.

Keep tightly coupled changes together. A signature change and required caller updates usually form one atomic change.
Apply the same rule to types, schemas, migrations, generated artifacts, and lockfiles.
Multiple later consumers can justify a shared prerequisite, but independent validity remains the deciding criterion.

For example:

```text
refactor(player): isolate playback state
feat(player): persist playback queue
```

This split works only if the refactor stands alone and preserves existing behavior.

### Tests and intermediate validity

Keep tests with the behavior they define or verify.
Separate tests when they have an independent purpose, such as coverage for behavior that already works.

Keep a failing regression test with its fix by default.
Use a separate intentionally failing test commit only when the user explicitly requests or approves that workflow.
Document this exception and its effect on CI and bisect.

Distinguish pre-existing failures from failures introduced by the proposed sequence.
Do not introduce new failures merely to make commits smaller.

### Corrections and review history

Distinguish committed history from uncommitted development:

- For an existing implementation commit, add corrections as new commits by default.
- For uncommitted work, split the final changes by purpose rather than recreating intermediate mistakes.

Do not reconstruct a defective version solely to produce a later corrective commit.
A correction to existing repository behavior can still be an independent fix.

Keep review responses visible as separate commits, including small fixes, until the user requests history changes.
Amend, squash, rebase, or apply fixups only with explicit authorization to rewrite the relevant history.
A generic request to clean code is not authorization to rewrite commits.

## 4. Present the review or plan

In review mode, report findings with locations, consequences, and proposed corrections. Stop without executing them.

In plan and execute modes, present a concise plan in oldest-to-newest order.
For each commit, include:

- proposed message and purpose
- files or selected changes
- dependencies on earlier commits
- role: prerequisite, implementation, independent change, or correction
- relevant validation

Use commit messages that describe intent. Include a body only when the subject and diff omit important context.

State which changes remain excluded and why.
In plan mode, stop here. In execute mode, proceed only within the authorized scope.

## 5. Execute and check each commit

Apply the safety rules below throughout execution.
If history rewriting is authorized, use a plan specific to the selected range instead of the new-commit procedure below.
Preserve a recoverable reference to the original history before rewriting it.

For each new commit:

1. Stage only its planned changes with explicit paths or selective staging.
2. Inspect the complete staged diff, including any changes staged before this task.
3. Check the candidate commit state for missing dependencies and unintended content.
4. Run relevant validation against that state when practical.
5. Create the commit only if the user authorized committing.
6. Inspect the resulting commit and remaining changes before continuing.

Useful inspection commands:

```bash
git diff --cached --stat
git diff --cached
git diff --cached --check
```

Use `git add -p` when interactive staging is available. Otherwise, use an explicit patch applied to the index.
Whole-file staging includes every unstaged change in that file, not only changes from the current task.
Use `git add .` or `git add -A` only after verifying that all included changes belong to this commit.

Validate the staged snapshot or committed tree in an isolated location when unstaged changes could conceal missing dependencies.
Tests against the complete working tree do not prove that an intermediate commit works.
If isolated validation is impractical, inspect dependencies and report which intermediate states remain untested.

Discover validation commands from repository configuration. Use focused tests, type checks, lint, or builds as appropriate.
If validation reveals a new failure, resolve the commit boundary or implementation issue before continuing.

After each commit, inspect:

```bash
git show --stat --oneline HEAD
git show HEAD
git status --short
```

If a hook changes files or a commit fails, inspect HEAD, the index, and the working tree.
Determine what happened before retrying. Recheck any changed candidate diff.
Stop when the authorized work is complete or a blocker requires user input.

### Safety

- Preserve unrelated files and their staged or unstaged state.
- Avoid destructive resets, restores, and cleanup on user work.
- Exclude secrets, credentials, debug artifacts, and unrelated modifications.
- Run hooks normally. Bypass them only when explicitly requested.
- Pause on unresolved conflicts or an unexpected Git operation already in progress.
- Ask before changing files when the request only authorizes organizing or committing existing changes.

## 6. Check and report the result

Inspect the entire resulting sequence in oldest-to-newest order, not only a fixed number of recent commits.
Check that each commit matches its planned purpose and follows its dependencies.
Compare the remaining files and staged changes with the recorded starting state.
Account for every intended change and preserve unrelated work.
Run relevant final validation when practical.

Report:

- resulting commit hashes and subjects, or staged changes for a staging-only request
- the reason for the grouping
- changes intentionally left uncommitted
- checks performed and any intermediate states not tested
- blockers, pre-existing failures, or approved exceptions

Keep the report compact. Distinguish completed actions from recommendations and unverified assumptions.
