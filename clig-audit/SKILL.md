---
name: clig-audit
description: Audit an existing CLI codebase against the Command Line Interface Guidelines and produce an evidence-based Markdown report without changing code.
disable-model-invocation: true
---

# CLIG Audit

Audit the CLI against the complete CLIG snapshot bundled with this skill. No network access is required to obtain the guidelines. The only final artifact is a Markdown report: recommend fixes, but do not implement them.

## 1. Establish scope

Use the project, commands, and report path supplied by the user. By default, audit the current project and write `clig-audit-report.md` in its root. If the file exists, choose an unused numeric suffix. Ask for clarification when a workspace contains multiple independent applications and the target is unclear.

Read project instructions, manifests, and launch/test documentation. Record the revision when available, initial working-tree state, platform, and environment limitations. Do not modify source code, tests, configuration, dependencies, lockfiles, or Git state. Run temporary probes and builds outside the working tree or in an isolated copy. Do not revert pre-existing changes.

Locate public entry points, command/subcommand registration, flags, the CLI framework and its version, handlers, shared output/error wrappers, configuration loading, prompts, lifecycle code, and tests. Include shell wrappers and child processes. Separate conventional CLI behavior from full-screen TUI internals: CLIG does not cover full-screen terminal interfaces.

**Completion criterion:** map entry point → parser → hooks → handler → output/error/exit, identifying shared implementations and divergent command paths. If no CLI exists, report that limitation instead of inventing findings.

## 2. Read the bundled recommendations

Read [source provenance and contents](references/clig-source.md), then the Introduction, Philosophy, and Guidelines preamble in the [complete local CLIG source](references/clig.md). Read every applicable guideline section in full, including its examples, qualifications, and exceptions, using the contents index to navigate. Continue local reads when tool output is truncated. Resolve these paths relative to this skill directory, not the audited project.

Use this snapshot as the authoritative audit baseline; the checklist is not a substitute. For each finding, verify the recommendation against the local text. Record the pinned revision and snapshot retrieval date from the provenance file, separately from the audit date. Cite the section name and pinned source; canonical https://clig.dev links are optional conveniences, not dependencies. Do not invent quotations or link anchors.

Do not fetch CLIG, check freshness, or follow external further-reading/media links during an audit. Refresh the snapshot only when the user explicitly requests a skill update, following the provenance file's refresh instructions. If bundled files are missing or unreadable, disclose an incomplete skill installation instead of silently falling back to the network or memory.

CLIG is guidance, not a mandatory standard: The Basics establishes essentials, while other sections include improvements and qualifications. Distinguish clear discrepancies with applicable recommendations from optional improvements and justified project trade-offs. Severity measures consequences, not the strength of a quotation.

**Completion criterion:** the baseline revision is recorded and the applicable local source sections have been read, or an incomplete-installation limitation is explicit.

## 3. Trace actual behavior

Read the [audit checklist](references/checklist.md) and investigate every applicable aspect. It is a research map, not an automatic list of violations. Track checked, unverified, and inapplicable aspects in working notes. Omit inapplicable items from the report; disclose applicable but unverified areas as limitations.

Use string searches for navigation, not conclusions. For each candidate finding, trace a reachable execution path from a public entry point to the observable outcome. Check global hooks, middleware, catches/finally blocks, logger transports, library settings, TTY branches, and child-process status propagation. Use semantic definitions/references/types, the installed dependency's source, or version-specific documentation when needed. Missing local code does not prove missing behavior: a framework or runtime may provide it.

Inspect every public command registration. Shared implementations may be tested through representative commands, but investigate overrides, destructive operations, and divergent error paths separately. Disclose sampling boundaries rather than calling a sampled audit exhaustive.

### Confirm safely through execution

When execution is feasible and helps establish behavior, use existing tests and narrow reproducible CLI invocations. Inspect startup and test scripts first: even `--help`, dependency installation, or tests may write files, access the network, or start services.

- Use temporary fixtures, isolated HOME/XDG/config/cache locations, and dummy credentials. Build in an isolated copy when artifacts are required.
- Do not operate on real resources, install/update software, transmit telemetry, or perform destructive actions merely to reproduce an issue. Use mocks or a local sandbox; otherwise rely on source evidence and disclose its limits.
- Capture stdout, stderr, and the CLI's own exit status separately. Do not attribute a pipeline consumer's, timeout wrapper's, or shell wrapper's status to the CLI.
- Compare TTY operation, redirected streams, closed stdin/EOF, and pipes where relevant. Use a PTY for terminal tests; ordinary tool runners usually are not terminals.
- Bound execution time and output size. Send signals only to processes/groups created by the probe. Check child-process termination and temporary-resource cleanup.
- Record each command, cwd, relevant environment/TTY conditions, outcome, and observation. Redact secrets before including evidence in the report.

**Completion criterion:** every applicable aspect is investigated or marked unverified; each candidate has a proven execution path or an observation with reproduction conditions. Inability to run the CLI is not itself a CLI finding.

## 4. Filter and prioritize

Include a finding only with a concrete location, proven current behavior, an applicable recommendation, and an explainable impact on users or automation. Static evidence is sufficient when the execution path is unambiguous; do not present it as runtime observation. Keep hypotheses in limitations, without severity or inclusion in finding counts.

Combine manifestations of one root cause into one finding listing affected commands. Do not repeat it under multiple themes. Do not replace a working parser/framework, rename interfaces, or recommend refactoring solely for stylistic preference. Missing JSON output, progress bars, man pages, `.env` support, or custom signal handlers is not universally a problem.

Classify findings separately from severity:
- **Type: discrepancy** — a confirmed clear mismatch with an applicable recommendation.
- **Type: improvement** — an optional improvement with a concrete benefit; normally severity `suggestion`.

Use this audit severity scale; do not attribute it to CLIG:
- **critical** — a confirmed path to substantial data loss/corruption, dangerous unintended action, or serious secret exposure. State conditions and scope; a flag named `password` alone is insufficient evidence for this severity.
- **major** — substantial breakage of a normal workflow, scripting/CI, or result correctness: for example, success status on failure, contaminated machine-readable stdout, or mandatory prompts without a non-interactive path.
- **minor** — a localized, confirmed usability/discoverability problem with a reasonable workaround that does not break the primary workflow.
- **suggestion** — a useful optional improvement without a proven substantial defect.

Recommend the smallest architecture-compatible change that fixes the cause. Name the relevant handler/helper or verified API of the installed library, expected behavior, and regression checks. If an API is unverified, describe the algorithm instead of inventing a call. Explain interface changes and effects on existing scripts; respect the project's compatibility policy rather than imposing compatibility layers. Explicitly describe any trade-off between that policy and CLIG.

**Completion criterion:** every finding has verified evidence, applicability, calibrated severity, no duplicate, and an actionable recommendation.

## 5. Write the report

Use the [report template](assets/report-template.md). Write the report in English unless the user explicitly requests another report language; preserve the required field names. Group findings by CLIG section or CLI aspect, ordered by severity within each group. Remove empty sections and placeholders.

Document scope, the CLIG source, the CLI map, executed checks, and limitations. Include every template field for each finding, with evidence and confirmation conditions. When there are no findings, explicitly state that no confirmed findings were identified within the checked scope; this does not prove complete compliance.

End with `Summary` and `Recommended order of fixes`. Count unique findings across all four severities, including zeroes, and distinguish optional improvements. Assess alignment qualitatively within the checked scope, without inventing a compliance percentage. Explain the fix order using data risk, usability, compatibility, scripting impact, and dependencies between fixes.

Verify file/line references, totals, and finding IDs in the fix order. Compare the working tree with its initial state: the new report should be the only persistent audit change. If execution unexpectedly produced other changes, disclose them rather than hiding or automatically reverting them. In the final response, provide the report path and a short summary, not a patch.
