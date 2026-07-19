---
name: cli-guidelines
description: >
  Design, implement, refactor, debug, or review command-line interfaces using
  the Command Line Interface Guidelines. Use this skill whenever work involves
  a CLI or command-line program: commands and subcommands, positional
  arguments, flags/options, help text, shell completion, stdout/stderr, exit
  codes, pipes, JSON/plain output, prompts, TTY detection, color and progress,
  configuration and environment variables, signals, destructive operations,
  distribution, telemetry, or backwards compatibility. Trigger for CLI code
  and UX work even when the user does not mention clig.dev or "CLI guidelines"
  explicitly. Do not use for ordinary shell usage questions or full-screen
  terminal UIs unless the task also changes a conventional command-line
  interface.
---

# CLI Guidelines

Build command-line programs as human-first interfaces that remain predictable Unix citizens. Optimize the interactive experience without corrupting automation contracts.

This skill is based on the open-source [Command Line Interface Guidelines](https://clig.dev/) by Aanand Prasad, Ben Firshman, Carl Tashian, and Eva Parish. The canonical source is [`content/_index.md`](https://github.com/cli-guidelines/cli-guidelines/blob/main/content/_index.md).

## Working Method

1. Inspect the existing interface before changing it: commands, aliases, flags, arguments, streams, exit codes, prompts, configuration, environment variables, machine output, docs, and tests.
2. Decide whether the CLI is new or already shipped. Treat shipped syntax, aliases, config keys, environment variables, and documented machine output as public APIs.
3. Classify each affected command: interactive or batch, read-only or state-changing, local or networked, pipe-oriented or human-oriented, safe or destructive.
4. Define the process contract before polishing text: exit status, `stdout`, `stderr`, input channels, TTY behavior, machine-readable output, interruption, and retry behavior.
5. Design human and automation behavior together. Prefer adaptive defaults based on TTY state plus explicit overrides such as `--json`, `--plain`, `--no-color`, `--no-input`, `--quiet`, and `--dry-run` where relevant.
6. Implement the smallest coherent change that follows the project's parser, command structure, naming, and output conventions. Do not bolt on a special case when a shared command or renderer owns the behavior.
7. Verify behavior by executing the CLI as a process, including redirected streams, non-interactive input, failures, and interruption. Unit tests alone often miss terminal contracts.

When requirements conflict, use this priority order:

1. Safety, correctness, and explicit user requirements.
2. Existing public compatibility commitments.
3. Automation contracts and composability.
4. Human usability and discoverability.
5. Familiar conventions and internal consistency.

Break a convention only when it demonstrably harms the intended users. Make the reason and compatibility impact explicit.

## Essential Contract

Treat these as blockers unless the runtime genuinely lacks the concept:

- Use a mature argument parser rather than hand-parsing tokens where practical.
- Exit `0` on success and non-zero on failure. Do not print an error and accidentally report success.
- Write primary results and machine-readable data to `stdout`.
- Write errors, warnings, progress, and explanatory messages to `stderr`.

Keep both streams intentional. `stderr` is not a license to dump raw logs, stack traces, or developer-only context by default.

## Human And Automation Modes

Use TTY detection as a heuristic, not as the only control surface:

- Interactive humans benefit from concise confirmation, progress, color, prompts, suggestions, and next steps.
- Pipelines and CI need deterministic output, no prompts, no animations, clean streams, bounded waits, and meaningful exit codes.
- Check TTY status per stream. Redirected `stdout` may need plain data while terminal-connected `stderr` can still show human messaging.
- Provide stable `--json` for structured data or `--plain` when human formatting breaks line-oriented processing.
- Keep human-readable output evolvable unless it was explicitly documented as stable. Direct automation toward machine modes.
- Never prompt when `stdin` is not a TTY. `--no-input` disables all interactivity; it does not mean "assume yes".

Read [references/interaction-design.md](references/interaction-design.md) when changing help, output, errors, flags, prompts, subcommands, color, paging, or progress.

## Safety And Reliability

- Validate inputs before side effects.
- Make unexpected file access and network activity explicit.
- Do not accept secrets in command-line flags or environment variables. Prefer protected files, standard input, sockets, OS credential stores, or a secret manager, chosen to fit the platform and automation model.
- Match confirmation strength to risk. Moderate destructive actions usually need confirmation and a dry run; severe actions should require target-bound confirmation such as `--confirm=<resource-name>` while remaining scriptable.
- Announce slow or networked work promptly, use reasonable timeouts, and show non-animated progress only when appropriate.
- Handle Ctrl-C quickly, bound cleanup, and make repeated invocation after interruption safe through idempotence, recovery, or resumption.
- Catch expected failures and explain what happened, why it matters, and how to recover. Hide stack traces unless debug information is requested or needed for an unexpected bug report.

Read [references/reliability-and-lifecycle.md](references/reliability-and-lifecycle.md) for robustness, signals, configuration, environment variables, compatibility, distribution, and telemetry.

## Help And Discovery Baseline

- Support `-h` and `--help` at every relevant command level, with no side effects and success status.
- Let help win over unrelated parse errors so appending `--help` remains useful.
- When a command requires arguments and has no useful default action, show concise help on bare invocation. Interactive-by-default commands are a valid exception.
- Lead help with a short purpose statement and realistic examples. Put common commands and flags before exhaustive listings.
- Suggest likely corrections rather than silently changing a state-changing command.
- Include a path to complete docs, support, or issue reporting.

## Compatibility Discipline

Before changing an existing CLI, identify what users may have scripted:

- Command and flag names, argument order, explicit aliases, exit codes, config paths and keys, environment variables, JSON fields, and documented plain output are compatibility surfaces.
- Prefer additive changes only when they keep the interface coherent; more flags are not automatically safer design.
- Deprecate breaking behavior with an in-program warning and a migration path available before removal.
- Avoid arbitrary command-prefix abbreviations and catch-all default subcommands because they consume future naming space.
- Preserve stable machine formats deliberately. Do not freeze incidental human whitespace and decoration without evidence that they are contractual.

## Verification

Build a focused test matrix from [references/verification-matrix.md](references/verification-matrix.md). At minimum cover:

- Bare command, `-h`, `--help`, subcommand help, and help mixed with invalid input.
- Successful and failed invocations with exact exit codes and stream separation.
- Piped or redirected `stdout`, redirected `stderr`, and non-interactive `stdin`.
- `--json` or `--plain` validity and stability where supported.
- TTY-sensitive color, progress, paging, prompts, `NO_COLOR`, `TERM=dumb`, and explicit overrides where relevant.
- Destructive-operation confirmation and non-interactive equivalents.
- Ctrl-C, timeout, partial failure, and safe retry for long-running or stateful commands.
- Configuration precedence and compatibility tests for changed public interfaces.

## Task-Specific Output

For implementation, make the code and tests conform to the contract, then report user-visible behavior and verification performed.

For design proposals, specify:

```markdown
**Interface**
Commands, arguments, flags, defaults, and examples.

**Process Contract**
stdin/stdout/stderr, exit codes, TTY and machine modes.

**Safety And Recovery**
Validation, destructive actions, timeouts, interruption, retries.

**Compatibility**
Existing commitments, migration, stable machine output.

**Verification**
Process-level scenarios and expected behavior.
```

For reviews, lead with concrete findings ordered by severity. Cite the affected command or `path:line`, explain the user or automation impact, and give a practical fix direction. Distinguish guideline preferences from correctness, safety, and compatibility defects.

## Reference Map

- [references/principles.md](references/principles.md): philosophy, decision tensions, and scope.
- [references/interaction-design.md](references/interaction-design.md): help, docs, output, errors, arguments, flags, interactivity, and subcommands.
- [references/reliability-and-lifecycle.md](references/reliability-and-lifecycle.md): robustness, future-proofing, signals, configuration, environment, naming, distribution, and analytics.
- [references/verification-matrix.md](references/verification-matrix.md): executable acceptance checklist.
- [references/sources.md](references/sources.md): source attribution, canonical links, and license notes.
