# CLI Verification Matrix

Select the rows relevant to the changed behavior. Run the executable as a subprocess so tests observe real streams, TTY detection, signals, and exit statuses.

## Core Process Contract

| Scenario | Expected evidence |
| --- | --- |
| Successful invocation | Exit `0`; primary result on `stdout`; no error text |
| Expected user failure | Non-zero exit; concise actionable message on `stderr`; machine `stdout` remains clean |
| Unexpected internal failure | Non-zero exit; bounded user message; debug or bug-report path without default secret leakage |
| Partial failure | Non-zero when required work failed; completed and failed items are distinguishable |
| Pipeline | Downstream command receives only primary data; diagnostics remain separately visible |
| Redirected `stdout` | No ANSI color or animation in that stream |
| Redirected `stderr` | No ANSI color in that stream; `stdout` behavior evaluated independently |

Capture streams separately in tests. A combined snapshot can hide the most important contract violation.

## Help And Discovery

| Scenario | Expected evidence |
| --- | --- |
| Bare command | Useful default action, interactive flow, or concise help when arguments are required |
| `-h` | Full help, exit `0`, no side effects |
| `--help` | Full help, exit `0`, no side effects |
| Help with invalid input | Help wins rather than an unrelated parse error |
| Subcommand help | Local purpose, usage, common options, examples, and docs path |
| Misspelled command | Clear unknown-command error and safe suggestion where confidence is high |
| Expected piped input but interactive `stdin` | Immediate guidance rather than an unexplained wait |

## Machine Output

| Scenario | Expected evidence |
| --- | --- |
| `--json` success | Valid JSON on `stdout`; no progress mixed in; documented schema fields present |
| `--json` failure | Documented failure contract and non-zero exit; no malformed partial success document |
| `--plain` | Undecorated predictable records, commonly one record per line |
| Human default | Readable and concise; does not claim to be the stable machine API |
| Quiet mode | Non-essential status hidden; failures and requested primary output preserved |

Validate JSON with a parser, not a text snapshot alone. Test field compatibility when changing an existing schema.

## Color, Progress, And Paging

| Scenario | Expected evidence |
| --- | --- |
| Terminal-connected stream | Intended color or progress may appear |
| Non-TTY stream | Color and animations disabled for that stream |
| Non-empty `NO_COLOR` | Color disabled |
| Empty `NO_COLOR` | Follow the documented policy; the source guideline only requires disabling for non-empty values |
| `TERM=dumb` | Terminal decoration disabled |
| `--no-color` | Color disabled regardless of TTY |
| Long interactive output | Pager only if intended and usable |
| Pipeline or CI | Pager never starts |
| Progress failure | Relevant hidden logs or failure details become visible |

Pseudo-terminals may be needed to test the TTY cases reliably.

## Input And Interactivity

| Scenario | Expected evidence |
| --- | --- |
| Missing value with TTY | Prompt only if prompting is part of the design |
| Missing value without TTY | No prompt; actionable failure naming the required non-interactive input |
| `--no-input` | No prompt under any circumstance |
| Password prompt | Typed value is not echoed |
| Standard input mode | `-` or documented stdin input composes without ambiguity |
| User cancels prompt | Clear cancellation result and intentional exit status |

Add a timeout to tests that assert a process does not prompt, so a regression fails instead of hanging the suite.

## Destructive Actions

| Scenario | Expected evidence |
| --- | --- |
| Dry run | Same validation and planning path; no mutation; accurate effect summary |
| Moderate action in TTY | Clear target and consequence; confirmation when required |
| Moderate action without TTY | No prompt; explicit force or confirmation mechanism required |
| Severe action | Target-bound confirmation such as the resource name |
| Wrong confirmation target | Safe failure with no mutation |
| Hidden destructive consequence | Plan detects and surfaces implicit deletions or replacements |
| Success | Exact resulting state is reported and inspectable |

Verify absence of side effects, not only printed text.

## Reliability And Signals

| Scenario | Expected evidence |
| --- | --- |
| Slow startup | Prompt acknowledgement or progress instead of unexplained silence |
| Network stall | Reasonable timeout and useful recovery message |
| Transient failure and retry | Safe continuation, resumption, or idempotent rerun |
| Ctrl-C during work | Fast acknowledgement, bounded exit, intentional code |
| Ctrl-C during cleanup | Cleanup is bounded; repeated interrupt can exit promptly |
| Next run after interruption | No permanent stale lock or corrupt state; recovery is clear |
| Concurrent invocations | No data corruption; output remains attributable and readable |

Use real signal delivery in integration tests where the platform permits it.

## Configuration

| Scenario | Expected evidence |
| --- | --- |
| Default only | Documented default is used |
| System config | Overrides default |
| User config | Overrides system config |
| Project config | Overrides user config |
| Environment | Overrides project config |
| Flag | Overrides environment |
| Invalid config | Names source and key; fails before mutation |
| Shared config modification | Requires consent and is reversible/uninstallable |

Test pairwise conflicts, not only each source in isolation.

## Compatibility

| Scenario | Expected evidence |
| --- | --- |
| Existing command and aliases | Continue to resolve or emit a planned deprecation warning |
| Existing flags and positionals | Preserve semantics or provide an active migration path |
| Existing JSON/plain schema | Compatible fields and types, or a versioned migration |
| Human output change | Machine consumers remain on a stable mode |
| Deprecated use | Warning explains replacement and removal timeline or release boundary |
| Migrated use | No obsolete warning where detection is practical |
| Unknown command prefix | Rejected unless it is an explicit alias |

## Distribution And Trust

| Scenario | Expected evidence |
| --- | --- |
| Fresh install | Expected executable and managed artifacts only |
| Uninstall | Executable, managed config, services, completions, and documented generated artifacts handled |
| First-run telemetry | No transmission before the documented consent behavior |
| Telemetry disabled | No analytics or crash transmission |
| Bug report generation | Sensitive values redacted and submission remains user-controlled |
