# CLI Reliability And Lifecycle

Use this reference for long-running commands, network operations, interruption, compatibility, configuration, environment variables, naming, distribution, and telemetry.

## Robustness

### Validate Before Effects

- Validate syntax, types, ranges, permissions, resource identity, and conflicting options before mutation.
- Collect independent validation errors when that helps users fix an invocation in one pass, but avoid overwhelming duplicate messages.
- Resolve the operation plan before applying it when practical. A dry run should exercise the same planning and validation path as the real command.

### Responsiveness And Progress

- Produce an initial response in roughly 100 ms when work will take longer. Responsiveness means acknowledging work, not necessarily completing it immediately.
- Announce network activity before waiting so latency does not look like a hang.
- Show meaningful progress for long work. Prefer units tied to actual work over indefinite spinners when totals are available.
- Avoid progress that appears stuck. Surface stage transitions or elapsed context for operations with uncertain duration.
- Disable animated rendering outside a suitable TTY.
- If a progress renderer suppresses normal logs, reveal the relevant logs when a task fails.
- Parallel work may improve speed, but prevent interleaved output from becoming unreadable. Group per-task messages or use a concurrency-aware renderer.

### Timeouts, Retries, And Recovery

- Set reasonable default network timeouts and allow configuration where workloads vary legitimately.
- Distinguish connection, operation, and cleanup timeouts when that improves recovery.
- Make retrying the same invocation safe. Prefer idempotence, resume tokens, checkpoints, or detection of already-completed work.
- Design for crash-only recovery where possible: minimize required shutdown bookkeeping and tolerate partial cleanup on the next start.
- Preserve clear outcomes under partial failure. Do not return success if required work failed.
- Assume bad networks, concurrent invocations, unusual filesystems, limited permissions, and unsupported environments will occur.

## Signals And Control Characters

- On the first Ctrl-C, acknowledge interruption and begin fast, bounded cleanup.
- Do not let cleanup make the process appear to ignore the user.
- On a second Ctrl-C, allow immediate exit or skip long cleanup after briefly explaining the consequence.
- Forward signals to child processes where the command acts as a wrapper, unless doing so would violate a documented control model.
- Test the next invocation after interruption. It must tolerate locks, temporary files, partial remote operations, or other unfinished state.

## Future-Proofing

Treat these as public interfaces once shipped or documented:

- Commands, subcommands, flags, positional meaning, aliases, and exit codes.
- Configuration locations, file formats, keys, and precedence.
- Environment variable names and semantics.
- JSON fields, plain-output records, and any human format explicitly promised as stable.

Evolution guidance:

- Prefer additive changes when they keep the model understandable.
- Before a breaking release, warn during normal use of the old form and provide a migration path users can adopt immediately.
- Stop warning users who have migrated when practical.
- Keep stable machine output separate from human output so usability can improve without breaking scripts.
- Do not route unknown first arguments into a catch-all default subcommand. A future subcommand could change the meaning of old invocations.
- Do not accept arbitrary unambiguous prefixes for commands. Explicit aliases are safe because they can be committed to permanently.
- Avoid critical runtime dependencies on hosted services that may disappear and make old installations unusable.

For existing CLIs, investigate actual compatibility evidence before redesigning: docs, completion scripts, issue reports, examples, tests, release notes, package consumers, and telemetry collected with consent.

## Configuration

Choose a channel based on scope and stability:

- Flags: invocation-specific choices such as dry run, output format, or debug mode.
- Environment variables: contextual differences between shells, machines, users, sessions, or projects, provided the value is simple and non-secret.
- User configuration: persistent preferences too complex or numerous for environment variables.
- Project configuration: stable settings shared by collaborators; use a dedicated, version-controlled format.
- System configuration: administrator defaults for all users.

Recommended precedence, highest first:

1. Command-line flags.
2. Current process environment.
3. Project configuration, including a project `.env` when appropriate.
4. User configuration.
5. System configuration.

Make precedence observable and test conflicts between layers.

- Follow XDG base-directory conventions where applicable, while respecting native conventions on supported platforms.
- Ask before changing another program's or system-wide configuration.
- Prefer a separate managed include file over appending opaque content to a shared file.
- If shared-file edits are unavoidable, mark ownership clearly and make uninstall cleanup safe.

## Environment Variables

- Use portable uppercase names containing letters, digits, and underscores, not beginning with a digit.
- Prefer single-line values.
- Do not redefine widely established variables with surprising semantics.
- Honor relevant ecosystem variables such as `NO_COLOR`, `DEBUG`, `EDITOR`, proxy variables, `SHELL`, terminal variables, `TMPDIR`, `HOME`, `PAGER`, `LINES`, and `COLUMNS` when they apply.
- Use `.env` for simple project-local context, not as an untyped replacement for structured configuration.
- Do not store secrets in environment variables. They can leak through inherited environments, process inspection, service managers, container metadata, diagnostics, and logs. Use a credential-specific channel.

## Naming

- Choose a memorable, searchable name that is not excessively generic.
- Prefer lowercase letters and use dashes only when needed.
- Keep frequently typed commands short enough for comfort, but do not consume an extremely short global name without strong justification.
- Check package registries, executable collisions, search results, pronunciation, and platform filesystem rules before committing.

## Distribution And Uninstallation

- Prefer a single binary when practical.
- Otherwise use the platform or language ecosystem's package mechanism rather than scattering unmanaged files.
- A developer tool may reasonably depend on the runtime of the ecosystem it serves.
- Minimize filesystem, startup-file, service, and privilege changes.
- Make uninstalling complete and easy. Document removal where dissatisfied users will find it, and account for generated config, caches, services, and completion files.

## Analytics And Trust

- Do not transmit usage, crash, or environment data without consent.
- Prefer explicit opt-in.
- If product policy uses opt-out, disclose collection prominently on first run or before installation and provide an easy disable mechanism.
- Explain exactly what is collected, why, how it is protected or anonymized, and how long it is retained.
- Never include arguments, paths, environment values, command output, or other potentially sensitive data by default.
- Consider lower-risk evidence first: documentation analytics, package download counts, interviews, support requests, and voluntary feedback.
