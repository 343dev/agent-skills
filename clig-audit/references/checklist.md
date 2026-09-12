# CLIG Audit Map

Verify conditions and exceptions against the [complete bundled CLIG source](clig.md), using the [source index](clig-source.md) to locate sections. No site fetch is needed. These are investigation areas, not mandatory features for every CLI.

| CLIG section | Trace and confirm |
| --- | --- |
| The Basics | Parser and settings; success/failure exit status, including asynchronous failures and subprocesses; primary results on stdout and diagnostics on stderr. Capture streams separately for success, invalid input, and execution failure. |
| Help | Root/subcommand `-h` and `--help`, help combined with other arguments; missing required input and the interactive-by-default exception; help availability without credentials/network; examples, descriptions, support/docs links. Inspect startup hooks before parsing and behavior when a pipe-oriented command receives terminal stdin. |
| Documentation | Documentation matches actual commands, flags, and defaults; terminal/web help and scripting instructions. Man pages are a conditional improvement, not a universal requirement. |
| Arguments and flags | Long forms, conventional names, required inputs, validation, ambiguous positionals, defaults, and global flag placement within parser constraints. For file I/O, investigate `-`; for dangerous actions, proportionate confirmation, dry-run behavior, and explicit non-interactive authorization. Trace secrets accepted through argv and safer alternatives. Explore `--` and filenames beginning with a hyphen as parser edge cases; link findings to an actual applicable recommendation rather than inventing a CLIG rule. |
| Subcommands | Consistent names, flag semantics, and output; ambiguous commands, shared flags, noun/verb conventions. Both noun/verb orders are acceptable: evaluate consistency, not taste. |
| Output | Human/machine modes, clean JSON/plain output, quiet/verbose behavior, and useful state-change feedback; separate stdout/stderr TTY detection; redirected color, nonempty `NO_COLOR`, `TERM=dumb`, explicit color disable, and documented force-color precedence. Animations/progress must not corrupt redirected output; inspect paging and interactivity controls. Emoji alone are not a defect. |
| Errors | Expected input, file, permission, and network errors: context, understandable cause, and recovery action; appropriate stack traces/debug details, duplicate diagnostics, secret disclosure. Follow exceptions to the outer handler and process exit. |
| Interactivity | stdin TTY checks, explicit non-interactive operation (`--no-input` where applicable), supplying required data without prompts, EOF, cancellation, and password echo. Non-interactive mode must not imply automatic consent to dangerous actions. Include library and subprocess prompts. |
| Robustness | Early validation before side effects; responsiveness/progress for long operations; finite network timeouts, bounded retries, partial failures, reruns, and recovery. Investigate atomicity/concurrency where real operations depend on them; do not require universal idempotence. Do not claim a timing threshold was exceeded without measurement. |
| Signals and control characters | Ctrl-C/SIGINT, immediate feedback, bounded cleanup, repeated interruption, and terminal-state restoration. For children, investigate forwarding, waiting, and status propagation. Examine SIGTERM where lifecycle warrants it, without presenting additional platform conventions as literal CLIG requirements. Runtime defaults may suffice without a custom handler. |
| Configuration | Actual sources, project/user/system scope, platform-appropriate XDG use; flags > shell environment > project config > user config > system config for the same setting. Inspect merge order, including false/zero/empty values and defaults. Changes to another program's configuration require appropriate consent, explanation, and minimal footprint. |
| Environment variables | Documented names/values, collisions, uppercase letters/digits/underscores; applicable editor, proxy, temporary-directory, home, pager, terminal-size, and color variables. Account for support inherited from libraries. `.env` is contextual, not mandatory. Trace actual consumption of secrets from environment variables and safer input mechanisms. |
| Future-proofing | Catch-all commands, arbitrary prefix abbreviations versus explicit aliases, stable machine output, deprecation/change policy, and hidden mandatory external dependencies. Prove cross-version changes with history/documentation; a snapshot does not establish a regression. Respect explicit project policy and explain conflicts with CLIG. |
| Naming | Confirmed executable collisions and practical usability problems; avoid subjective branding preferences. |
| Distribution | Actual install/uninstall flow, footprint, platform packaging conventions, and prerequisites. Single binaries are recommended “if possible”; language-specific tools may assume their runtime. Test installation only in isolation. |
| Analytics | Startup/background network calls, consent/disclosure, disable paths, collected data/redaction, and effects of telemetry failure on primary operations. CLIG discusses both opt-in and disclosed, easily disabled opt-out; do not automatically flag all opt-out implementations. Do not transmit real data to prove behavior. |

## False-positive checks

- A local print call may emit the intended primary result, not diagnostics: inspect its purpose and final destination.
- A handler's return value is not necessarily the process exit status: inspect the entry point and runtime.
- Missing local TTY checks do not prove missing detection: inspect libraries and settings.
- A failed launch caused by missing audit-environment dependencies does not establish an application defect.
- Do not demand JSON, subcommands, configuration, animation, or prompts from a minimal text filter without a concrete need.
