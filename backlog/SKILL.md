---
name: backlog
description: Manage deferred engineering work in a Git repository using one Markdown file per issue in docs/backlog/. Use to list, add, update, triage, resolve, or clean up backlog items, and to record confirmed out-of-scope findings without expanding the current task.
---

# Backlog

Maintain lightweight, maintainer-only project memory in `docs/backlog/`. This is not a ticket system. Keep unrelated work outside the current task. Copy entries into external tickets, PRs, or review comments only on request.

## Scope and authorization

- Require a Git repository and filesystem access. Locate the repository root with `git rev-parse --show-toplevel`. Resolve backlog paths from that root. If these requirements are unavailable, explain the limitation and stop.
- Interpret user intent, not command spelling. Reading and recommending do not authorize changes.
- Act on explicit instructions without asking for the same permission twice. Ask when the action or an important choice is ambiguous, or when branch safety requires confirmation.
- Discovering a problem does not authorize recording or fixing it. Propose an entry only for a concrete, credible, nontrivial issue outside the current scope. Continue the current task without interruption for speculative or cosmetic findings.
- Follow existing project implementation, review, and commit policies.

## Choose the operation and scope

Determine the operation first, then its scope: one item, selected items, or all items.

- An explicit operation takes precedence over defaults. “List all” means list, “review all” means review, and “fix all” authorizes fixes.
- With no operation, a specific slug means review that item. A bare `--all` means interactive triage of all items. Otherwise, list the backlog.
- A read-only review ends with recommendations. Interactive triage asks for a decision after each item.
- For an operation that needs a target, ask if the target is unclear. If a specified slug is missing, report it and show available slugs.
- Apply the [multiple-item workflow](#multiple-items) when the scope includes more than one item.

## Storage and format

Store one distinct problem per file: `docs/backlog/<problem-slug>.md`. Use stable, lowercase kebab-case slugs that describe the issue rather than only its location.

```markdown
---
worth: "yes"
priority: "normal"
where: src/audio/player.ts:42
added: "2026-10-22"
---

# Audio playback fails in Safari

Describe the symptoms, impact, evidence, and reason for deferral.
```

| Field | Rule |
| --- | --- |
| `worth` | Required: `"yes"` (worth doing), `"maybe"` (value uncertain), or `"no"` (intentionally rejected). Quote values for YAML parser portability. |
| `priority` | Optional: `"high"`, `"normal"` (default), or `"low"`. Urgency is independent of `worth`. |
| `where` | Optional `path[:line]`, relative to the repository root. This is a navigation hint, not an identifier. |
| `added` | Required quoted `"YYYY-MM-DD"`. Use the creation date. Preserve it during updates and renames. Merges retain the earliest source date. |

Use the H1 as the title. Keep enough context to understand the issue months later. For `maybe`, state what evidence would settle the decision. For `no`, explain the rejection. `yes` does not authorize an immediate fix.

Use Git history instead of `done` or `closed` states. Remove resolved items through **Fix**, or explicitly discarded items through **Drop**. Retain `worth: "no"` when the rejection rationale remains useful.

## Operations

### List

Read frontmatter and titles from `docs/backlog/*.md`. Keep listing read-only, without inspecting source code or validating `where` locations.

- If the directory is absent or empty, report an empty backlog.
- Show slug/title, `worth`, `priority` when present, `added`, and `where` when present.
- Group by `worth`: `yes`, `maybe`, `no`. Within each group, sort by priority (`high`, `normal`, `low`), then oldest `added` first.
- Show malformed records separately, with their paths and metadata errors. Preserve readable fields without inventing missing required values or repairing files.
- Treat staleness as **unknown** until investigated. Metadata age alone does not establish whether an issue still exists.

### Review / triage

1. Read the complete backlog file.
2. Check its assumptions against relevant **current** code. Use `where` as a starting point when present. Search narrowly for the underlying behavior if the location moved or disappeared.
3. Report:
   - **Summary:** the current problem and evidence, or why the item may be stale.
   - **Effort:** likely changes, relevant tests, and uncertainty.
   - **Blast radius:** affected code paths or behavior.
   - **Materiality:** who or what is affected, and the cost of leaving the issue.
4. Recommend **fix**, **drop**, or **leave**, with rationale. For interactive triage, ask for a choice unless the user already specified the action.

Keep the investigation proportional to the issue. Reviewing an item does not authorize modifying it.

### Add

1. Check that the finding is concrete rather than speculative. Search existing titles, slugs, `where` paths, and descriptions for duplicates. Compare underlying causes, not just filenames.
2. If an item already exists, point to it. Update it only if the user requested recording new information or explicitly authorized an update.
3. Apply [write safety](#write-safety). Check the target path for collisions. Create `docs/backlog/` only when writing the first actual item.
4. Write the smallest useful entry with supporting facts, impact, reason for deferral, and a way to verify or revisit it.

### Update, rename, or merge

- Change content or metadata as requested, following the storage rules.
- Check destination paths before renaming or merging.
- Merge entries only when they describe the same root issue or the user explicitly requests consolidation. Preserve relevant evidence from each source.
- Apply [write safety](#write-safety) to every affected source and destination.

### Fix

1. Read the item and check its assumptions against current code. If already resolved, report the evidence. Delete the item only if the user requested resolution or cleanup.
2. Otherwise, implement through the project's ordinary coding workflow. Add or update relevant tests and run appropriate validation.
3. Delete the backlog file only after verifying resolution and applying [write safety](#write-safety). Keep the item if the fix fails or remains unverified.

### Drop / leave

- **Drop:** on explicit request, apply [write safety](#write-safety) and delete only the specified backlog items. Leave code unchanged. If the user wants to retain a rejection rationale, update `worth` to `"no"` instead.
- **Leave:** make no changes. A request to set `worth: "no"` is an update, not leave.

### Multiple items

- For listing, return the full requested list without source investigation.
- For review, triage, or fixes, first scan the selected items for obvious dependencies. Investigate each item in detail when its turn comes.
- For read-only review, return a complete report for the requested scope. Do not pause after each item to request permission for recommended changes.
- For interactive triage, present one briefing and decision at a time.
- For an explicitly requested batch action, proceed without redundant approvals. Respect blockers, test failures, and write safety. Report skipped items and their blockers.
- Recheck later items when earlier changes affect them. Multiple-item scope alone does not authorize fixes or deletion.

## Write safety

### Branch safety

Apply this check before backlog-only add, update, rename, merge, or drop operations. A fix on the user's current working branch can remove its resolved item without a second branch confirmation.

1. Check the current branch with `git branch --show-current`. An empty result means detached HEAD.
2. Use an explicit project default-branch setting when available. Otherwise, read `git symbolic-ref --quiet refs/remotes/origin/HEAD`. Treat missing or conflicting evidence as an unknown default branch. Branch names such as `main`, `master`, and `trunk` do not establish the default.
3. On the confirmed default branch, proceed. Otherwise, state the branch situation and ask whether to write **in this checkout**. Skip this question if the user already explicitly authorized that location.
4. Switch branches or create worktrees only on explicit request.

### File and index safety

- Before editing, renaming, or deleting a backlog file, inspect `git status --short -- <path>`. Preserve pre-existing user changes. Obtain explicit approval for any operation that would overwrite or discard them.
- Use filesystem create, edit, and delete operations by default. These operations leave the index unchanged. `git rm` stages deletion, so use it only when staging was requested. Delete untracked files through the filesystem.
- Stage, commit, or push only on explicit instruction. Stage only the exact files involved. Before committing, ensure unrelated staged changes cannot enter the commit.
- A backlog-only commit contains no unrelated code. A fix commit includes its corresponding backlog deletion in the same logical commit.
- After changes, report exact paths, validation results or limitations, skipped actions, and whether changes are staged or committed.
