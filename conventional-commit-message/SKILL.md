---
name: conventional-commit-message
description: Create high-quality Git commit messages that follow Conventional Commits 1.0.0. Use this skill whenever the user asks for a commit message, commit title, squash merge message, changelog-friendly commit, semantic-release-friendly commit, or asks to rewrite/check a message against Conventional Commits, even if they only say "write commit msg", "name this commit", "сообщение коммита", or "conventional commit". Also use it when inspecting staged or unstaged git changes to summarize them as a proper Conventional Commit.
---

# Conventional Commit Message

Use this skill to generate or review commit messages according to Conventional Commits 1.0.0.

## Core Format

Use this structure:

```text
<type>[optional scope][optional !]: <description>

[optional body]

[optional footer(s)]
```

The first line is the commit header. It is the most important part because tools parse it for release notes and semantic versioning.

## Workflow

1. Inspect the user's change description, diff, staged files, branch name, issue reference, or existing message.
2. Identify the main user-visible intent of the change.
3. Choose the type that best matches release semantics.
4. Add a scope only when it helps locate the affected area.
5. Add `!` and/or a `BREAKING CHANGE:` footer when behavior or API compatibility changes.
6. Return a ready-to-use commit message. Do not run `git commit` unless the user explicitly asks.

If the change mixes unrelated intents, suggest splitting commits. If the user still needs one message, choose the dominant release-relevant intent and mention the tradeoff briefly.

## Type Selection

Prefer these common types:

- `feat`: new feature or capability for users; maps to SemVer minor.
- `fix`: bug fix; maps to SemVer patch.
- `perf`: performance improvement without API behavior change.
- `refactor`: internal restructuring without feature or bug-fix intent.
- `docs`: documentation-only change.
- `test`: tests only, or test infrastructure with no production behavior change.
- `build`: build system, dependencies, packaging, or artifact generation.
- `ci`: CI/CD pipeline changes.
- `style`: formatting or code style only, no behavior change.
- `chore`: maintenance that does not fit better elsewhere.
- `revert`: reverts prior changes.

When in doubt between `feat` and another type, use `feat` only if users receive a new behavior or capability. When in doubt between `fix` and `refactor`, use `fix` only if the change corrects wrong behavior.

## Scope Guidance

Use a short noun in parentheses when it adds useful context:

```text
feat(auth): add password reset flow
fix(parser): handle escaped delimiters
docs(readme): clarify setup steps
```

Good scopes are subsystem, package, module, route, CLI command, or config area names. Avoid scopes that are too broad (`app`, `code`) or too noisy (`misc`, `stuff`). Omit scope when no clear scope exists.

## Description Style

Write the description as a concise imperative phrase:

- Good: `fix(api): reject expired tokens`
- Good: `feat(cli): add dry-run option`
- Avoid: `fixed token bug`
- Avoid: `adds a new CLI option`

Keep the header short enough for terminal and GitHub views, ideally under 72 characters when practical. Do not end the header with a period.

## Breaking Changes

Breaking changes must be explicit because release tooling treats them as major-version signals.

Use `!` in the header for a compact signal:

```text
feat(api)!: require signed webhook payloads
```

Use a footer when the impact needs explanation:

```text
feat(api)!: require signed webhook payloads

BREAKING CHANGE: unsigned webhook requests now return 401.
```

`BREAKING CHANGE:` must be uppercase and followed by colon-space. `BREAKING-CHANGE:` is also valid, but prefer `BREAKING CHANGE:` because it is more widely recognized by humans.

## Body And Footers

Use a body when the reason or migration detail matters. Separate it from the header with one blank line.

Use footers for issue references, reviewers, co-authors, or breaking changes:

```text
fix(auth): refresh tokens before expiry

Refresh tokens proactively to avoid request failures during long sessions.

Refs: #123
```

Footer tokens use `-` instead of spaces, except `BREAKING CHANGE`.

## Output Modes

Default: output only the commit message in a code block.

If the user asks for options, provide 2-3 alternatives and label the recommended one.

If reviewing an existing message, show whether it conforms, then provide the corrected message.

If changes are unclear, ask one short clarifying question. If enough context exists, do not ask; make the best specific message.

## Examples

Input: Added OAuth login endpoints and UI.

Output:

```text
feat(auth): add OAuth login
```

Input: Fixed crash when config file is missing.

Output:

```text
fix(config): handle missing config file
```

Input: Removed the `legacyToken` field from public API responses.

Output:

```text
feat(api)!: remove legacy token from responses

BREAKING CHANGE: API responses no longer include the `legacyToken` field.
```

Input: Updated README typo and CI cache config in one diff.

Output:

```text
ci: update cache configuration
```

Consider splitting the README typo into a separate `docs` commit if practical.
