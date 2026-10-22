---
name: reviewable-commits
description: >
  Use whenever creating, planning, organizing, splitting, staging, reviewing,
  or otherwise working with Git commits for changes made during the current
  task. Analyze conversation context and the working tree, split changes into
  the smallest independently reviewable logical commits, order them by
  dependency, keep prerequisite and corrective changes separate, avoid mixing
  unrelated changes, and preserve review history.
---

# Reviewable Commits

Turn the current working tree into a clear, fine-grained sequence of logically ordered commits that are easy to review by a human.

The primary goals are:

- preserve the real logical history of the work
- keep commits as small as reasonably possible
- never mix unrelated logical changes
- make prerequisite changes explicit
- keep corrective commits visible during review
- order commits so each step is understandable in sequence

## Core principle

Split the task into the smallest independently understandable logical changes.

Prefer more commits over fewer commits when changes can be separated without making individual commits incomplete, misleading, invalid, or impossible to review meaningfully.

A commit should represent exactly one logical step in the work.

Do not combine changes merely because:

- they touch the same file
- they belong to the same feature
- they were implemented at the same time
- they use the same Conventional Commit type
- combining them would reduce commit count
- they live in the same directory or layer

When two changes can be reviewed independently, they should normally be separate commits.

## Use conversation context

Before inspecting Git, reconstruct the intent of the current work session from the conversation when that context is available.

Identify:

1. what the user originally requested
2. which implementation decisions were made
3. which intermediate approaches were abandoned
4. which changes are prerequisites or supporting refactors
5. which changes implement the requested behavior
6. which fixes were introduced during implementation
7. which fixes were introduced in response to review or later inspection

Conversation context describes **why** the changes exist.

Git diff describes **what** changed.

Use both.

Do not commit temporary experiments, abandoned approaches, debug code, accidental formatting changes, generated junk, or unrelated modifications merely because they exist in the working tree.

If an uncommitted change appears unrelated to the current task, leave it untouched unless its relationship is clear.

## Inspect the repository

Start by understanding the current state:

```bash
git status --short
git diff --stat
git diff
git diff --cached
git log --oneline -12
```

Inspect additional context when useful:

```bash
git diff -- <path>
git blame <path>
git log --oneline -- <path>
```

Determine the repository's existing commit message style from recent history.

Prefer matching the repository's established style over imposing a new convention.

## Build semantic change groups

Partition the diff into logical work units.

For every candidate group, answer:

- What single logical change does this group represent?
- Can a reviewer describe this commit in one sentence?
- Can it be reviewed independently?
- Does it depend on another uncommitted change?
- Does it modify existing code only to prepare later work?
- Is it a correction to an earlier change?
- Does it contain anything unrelated?
- Would separating it improve review clarity?

Use the **bisect test**:

> If this commit were the first bad commit found by `git bisect`, would its diff be a coherent unit that a developer could understand and fix in isolation?

If not, regroup it.

Use the **review boundary test**:

> Can a reviewer understand and evaluate this part of the work independently?

If yes, it should normally be its own commit.

When uncertain, prefer splitting rather than combining.

## Commit granularity

Create as many logical commits as reasonably possible.

Smaller commits are preferred because they:

- reduce review scope
- make intent easier to understand
- expose dependency structure
- make regressions easier to locate
- preserve the development history
- keep corrective work visible

Do not artificially split a single inseparable logical change.

Do not create meaningless micro-commits that contain incomplete fragments with no independent purpose.

The correct boundary is the smallest coherent logical unit.

## Unrelated and prerequisite changes

Do not modify files or code unrelated to the task.

If completing the task requires changing existing code that is not itself part of the requested behavior, make that change in a separate prerequisite commit.

Examples:

- prerequisite refactor
- cleanup required before implementing the feature
- correction of an existing abstraction
- type or API adjustment required by later work
- migration of existing code necessary for the new implementation

These changes must not be hidden inside the primary feature commit.

Prefer:

```text
refactor(core): decouple playback state
feat(player): add persistent playback queue
```

over one feature commit containing both the refactor and the new behavior.

If a prerequisite itself contains multiple independently reviewable logical changes, split those as well.

## Tests

Tests should follow the same logical-boundary rules as production code.

A test belongs in the same commit as the behavior it directly defines or proves when separating them would make the implementation commit incomplete or insufficiently verifiable.

A test may be a separate commit when it is itself a distinct logical step, for example:

- adding regression coverage for an already-existing bug before fixing it
- adding broader missing coverage independently of the implementation
- introducing reusable test infrastructure
- adding review-requested coverage after the main implementation commit

Do not create a generic "all tests" commit containing tests for multiple unrelated changes.

## Types, schemas, contracts, and migrations

Do not automatically group all type, schema, contract, or migration changes together.

Keep them with the logical change they serve unless they are a genuine prerequisite used by multiple later commits.

A shared foundation may be separate when doing so improves the dependency sequence and reviewability.

## Split files by hunks when necessary

A single file may contain changes belonging to different commits.

Do not force the entire file into one commit.

Use patch staging when needed:

```bash
git add -p
```

or equivalent explicit patch/index manipulation.

Be conservative when splitting adjacent hunks whose separation would make either commit misleading, incomplete, or broken.

Every changed hunk should belong to exactly one planned commit or be intentionally left uncommitted.

## Order commits by dependency

Build a dependency graph between semantic groups.

Place prerequisites before dependents.

A typical progression may be:

1. prerequisite fixes or refactors
2. shared contracts / schemas / types
3. low-level implementation
4. higher-level behavior
5. integration / API / UI wiring
6. independent fixes
7. documentation

This is not a mandatory layer-by-layer ordering.

Logical dependency and review clarity have higher priority than architectural layer.

Do not place a dependent commit before the change it requires.

## Corrective commits during implementation and review

Preserve meaningful corrective commits instead of hiding them inside earlier commits.

Examples:

```text
feat(player): persist playback queue
fix(player): handle unavailable restored tracks
```

A substantial correction should remain visible as a separate commit because it records an actual logical step in the work.

Do not automatically amend, squash, fixup, or fold substantial corrections into earlier commits.

This applies especially when the correction:

- changes behavior
- corrects an incorrect implementation assumption
- addresses review feedback
- adds missing validation
- changes a contract
- modifies nontrivial control flow
- affects multiple files

## Small review fixes

Small corrective commits should normally remain separate until review is complete.

Examples:

```text
fix(player): handle empty restored queue
fix(types): narrow playback status
style(player): simplify queue condition
```

Do not automatically squash these into their parent commits while review is ongoing.

Keeping them separate lets reviewers see exactly what changed in response to feedback.

After review is complete, commits may be reorganized, squashed, or fixup-applied only if explicitly requested.

## Do not rewrite review history automatically

During normal task work:

- do not amend earlier commits
- do not autosquash fixups
- do not interactively rebase completed commits
- do not rewrite published history
- do not hide substantial corrections inside earlier commits

Only rewrite history when the user explicitly asks for cleanup, squash, rebase, fixup, or final-history polishing.

## Prepare a commit plan

Before committing, produce a concise plan in oldest-to-newest order.

Example:

```text
1. refactor(player): isolate playback state
   - move state ownership into PlaybackStore
   - no behavior change
   - prerequisite for queue persistence

2. feat(player): persist playback queue
   - add queue persistence
   - restore queue on startup
   - include focused implementation tests

3. fix(player): skip unavailable restored tracks
   - handle removed catalog entries
   - preserve this as a separate corrective step

4. test(player): cover empty restored queue
   - add review-requested regression coverage
```

For each planned commit include:

- proposed message
- purpose
- files or hunks involved
- dependency on earlier commits, if any
- whether it is a prerequisite, primary implementation, or corrective commit

Before executing, verify internally that:

- every intended changed hunk belongs to exactly one planned commit
- unrelated changes are excluded
- no logical boundary has been unnecessarily collapsed

## Create commits

Stage only the changes belonging to the current planned commit.

Verify the staged diff before every commit:

```bash
git diff --cached --stat
git diff --cached
```

Then commit.

Never use:

```bash
git add .
git add -A
```

unless every uncommitted change has been verified to belong to that single commit.

Prefer explicit paths or patch staging.

After each commit, inspect:

```bash
git status --short
git show --stat --oneline HEAD
```

When useful, also inspect:

```bash
git show HEAD
```

Continue until all intended task changes are committed.

## Validate the sequence

After creating the commits, inspect the resulting history:

```bash
git log --oneline --decorate -10
git status --short
```

Review the sequence from oldest to newest.

Check that:

- each commit has exactly one clear logical purpose
- commits are as small as reasonably possible
- unrelated logical changes are not mixed
- prerequisite changes appear before dependents
- corrective changes remain visible
- commit messages describe intent
- no commit relies on unexplained future changes
- unrelated working-tree changes remain uncommitted
- no intended task changes were lost

If practical, run the repository's relevant validation commands after the final sequence:

- tests
- typecheck
- lint
- build

Use repository-specific commands discovered from project configuration.

## Commit message rules

Match the repository's existing convention.

If the repository clearly uses Conventional Commits, follow it.

Prefer messages describing the resulting logical change:

```text
refactor(player): isolate playback state
feat(player): persist playback queue
fix(player): skip unavailable restored tracks
test(player): cover empty restored queue
```

Avoid vague messages:

```text
update stuff
fix tests
cleanup
changes
refactor code
misc fixes
```

Keep subjects concise.

Use commit bodies only when they add useful context that is not obvious from the diff.

## Safety

Never:

- reset or discard unrelated user changes
- use `git checkout --`, `git restore`, `git reset --hard`, or destructive cleanup on user work
- silently include unrelated modifications
- commit secrets or credentials
- bypass hooks unless explicitly requested
- rewrite existing commits unless explicitly requested

If hooks modify files during commit, inspect those changes before continuing.

If a commit fails, diagnose the failure and preserve the working tree.

## Final report

After completion, report the resulting sequence in oldest-to-newest order:

```text
abc1234 refactor(player): isolate playback state
def5678 feat(player): persist playback queue
987abcd fix(player): skip unavailable restored tracks
456efgh test(player): cover empty restored queue
```

Briefly mention:

- why this grouping was chosen
- whether anything was intentionally left uncommitted
- validation performed
- any notable caveats

Keep the report compact.

## Final decision rule

When uncertain whether two changes belong together, ask:

> Is there a meaningful logical boundary between these changes that would help a reviewer understand the work?

If yes, separate them.

Combine changes only when separating them would make one of the commits incomplete, misleading, broken, or impossible to review meaningfully.
