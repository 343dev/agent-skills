# CLI Interaction Design

Use this reference when designing or reviewing help, documentation, output, errors, arguments, flags, prompts, color, progress, paging, and subcommands.

## Help

- Provide full help for `-h` and `--help` at each relevant command level.
- Reserve `-h` for help.
- Make help succeed and avoid side effects, including when other supplied arguments are invalid.
- For Git-like command trees, consider `app help`, `app help subcommand`, and `app subcommand --help` in addition to flags.
- If a command requires input and has no useful default behavior, show concise help on bare invocation. An interactive default flow is a valid exception.
- Concise help should identify purpose, show one or two realistic invocations, mention essential options, and point to full help.
- Lead full help with useful examples, especially common multi-flag workflows. Put exhaustive examples in web docs, tutorials, or a dedicated examples command.
- Put common commands and flags before rarely used ones.
- Use scannable headings and spacing. Never leak ANSI escape sequences when help is redirected or paged through a non-color-aware path.
- Link to version-appropriate web documentation and a support or issue-reporting path.
- If a misspelling is likely, suggest the intended command. Avoid silently executing a correction, especially for mutations.
- If a command expects piped input but receives an interactive `stdin`, explain the expected input immediately instead of hanging indefinitely.

## Documentation

- Provide searchable, linkable web documentation.
- Provide terminal-accessible, version-matched documentation that works offline.
- Consider man pages, but do not make them the only terminal documentation path because they are neither universal nor familiar to every user.
- Keep examples, help, and docs consistent with actual parser behavior and current version.

## Output Contracts

### Streams

- Primary content belongs on `stdout`.
- Errors, warnings, progress, and explanatory messaging belong on `stderr`.
- Machine-readable output must remain parseable by itself. Do not mix status messages into JSON or line records.
- Edit default `stderr` for users. Raw log levels, timestamps, subsystem names, and stack traces belong behind verbose or debug modes unless they are directly useful.

### Human And Machine Formats

- Optimize default terminal output for people.
- Preserve ordinary line and pipe behavior where possible so tools such as `grep`, `sort`, and `awk` remain useful.
- Add `--plain` when tables, wrapping, headings, or multi-line records make the human format unsuitable for scripts.
- Add `--json` when users need structured data. Produce valid JSON with a deliberate schema and compatibility policy.
- Do not imply that human formatting is byte-stable unless that is a documented contract.
- Briefly confirm success when silence would create doubt, especially for slow work or state changes.
- If state changed, explain the meaningful result and provide a way to inspect current state.
- Suggest logical next commands when they are part of a common workflow.
- Make external effects such as contacting a server or reading/writing an unrequested user file explicit. Internal caches do not need noisy disclosure on every invocation.

### Color, Symbols, And Layout

- Use color to encode a small number of meaningful distinctions; never rely on color alone.
- Determine color per output stream.
- Disable color for a stream when it is not a TTY.
- Disable color when `NO_COLOR` is set to a non-empty value, `TERM=dumb`, or `--no-color` is passed.
- Consider a tool-specific color setting and `FORCE_COLOR` only when semantics are documented and consistent with the ecosystem.
- Disable animations when `stdout` is not a TTY. Static progress messages on `stderr` may still be useful.
- Use symbols or emoji only when they improve scanning or meaning and have acceptable fallback behavior across supported terminals.
- Use compact ASCII encodings when they increase information density without requiring hidden knowledge for basic use.

### Paging

- Consider a pager for long interactive output.
- Start it only in an appropriate terminal context, never in pipelines or CI.
- Preserve formatting, exit behavior, Ctrl-C, and short-output ergonomics.
- Prefer a mature pager integration over ad hoc terminal process control.

## Errors

- Catch expected failures and rewrite them in user terms.
- State what failed, include the relevant object or value, and offer a concrete recovery action when known.
- Keep signal high. Group repeated failures under one explanation rather than printing the same message many times.
- Place the most actionable conclusion where it will be noticed, commonly at the end after concise context.
- Use strong color sparingly; users' eyes will be drawn to it.
- Separate user mistakes, environmental failures, transient remote failures, and internal bugs because their recovery paths differ.
- For unexpected bugs, provide access to debug details and an easy reporting path. Prefer a debug file or opt-in traceback over flooding the normal terminal.
- Pre-populate bug-report context where practical, but do not include secrets or transmit data without consent.

## Arguments And Flags

- Prefer flags for values representing distinct concepts.
- Use positional arguments for the primary object of an action or repeated homogeneous objects.
- Be suspicious of two or more positionals with different meanings. Keep them only when the operation is conventional and memorable.
- Provide long names for flags. Reserve short aliases for common operations.
- Follow established names when their semantics match:

| Flag | Meaning |
| --- | --- |
| `-a`, `--all` | Include all items |
| `-d`, `--debug` | Debug information |
| `-f`, `--force` | Bypass an ordinary safety check or force an operation |
| `-h`, `--help` | Help only |
| `-n`, `--dry-run` | Describe effects without applying them |
| `--no-input` | Disable every prompt and interactive element |
| `-o`, `--output` | Output destination |
| `-p`, `--port` | Network port |
| `-q`, `--quiet` | Suppress non-essential output |
| `-u`, `--user` | User identity |
| `--json` | Structured JSON output |
| `--version` | Program version |

`-v` is ambiguous between version and verbose. Use it only when project or ecosystem convention resolves the ambiguity.

- Choose useful defaults rather than requiring permanent aliases or repeated flags from most users.
- If optional input is omitted, prompt only in an interactive context and always expose a non-interactive form.
- For file inputs and outputs, support `-` as standard input or standard output when it composes naturally.
- For an optional value that needs an explicit disabled state, accept a clear token such as `none` instead of an ambiguous empty value.
- Make global flags, subcommands, and arguments order-independent where the parser reasonably supports it.
- Do not read secrets from flags. Process arguments can appear in shell history, process listings, debug output, and audit systems. Shell substitution does not remove this risk.

## Dangerous Actions

Classify the actual consequence, including implicit deletions caused by configuration changes.

- Mild: small, local, obvious, or easily reversible. An explicitly named delete command may not need another prompt.
- Moderate: broad local changes, remote mutation, bulk modification, or difficult rollback. Usually confirm and offer `--dry-run`.
- Severe: deletion of a complex remote resource or similarly irreversible loss. Require non-trivial, target-bound confirmation such as typing the resource name or passing `--confirm=<name>` non-interactively.

Do not treat `--force` and `--confirm=<target>` as equivalent. The latter proves that the caller identified the exact severe target.

## Interactivity

- Prompt only when `stdin` is a TTY.
- When `--no-input` is present, never prompt. If data is missing, fail with the exact flag, file, or input channel needed.
- Never interpret `--no-input` as approval for destructive work.
- Disable terminal echo for password entry.
- Make every interactive flow escapable and explain non-obvious escape sequences.
- Preserve Ctrl-C during network waits and child-process execution where possible.

## Subcommands

- Introduce subcommands when they organize meaningful complexity or combine closely related tools.
- Keep flags, output, configuration, help structure, and verbs consistent across subcommands.
- Noun-verb and verb-noun structures can both work. Choose one model for similar resources and operations.
- Avoid nearly synonymous sibling names such as `update` and `upgrade` unless the distinction is obvious and consistently taught.
