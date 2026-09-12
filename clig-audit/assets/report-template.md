# CLIG Audit: <project>

## Scope and method

- **Project / revision:** <path, revision, initial working-tree state>
- **Environment:** <platform, runtime/framework versions, relevant constraints>
- **Scope:** <entry points, commands, shared paths, sampling boundaries>
- **CLIG baseline:** <Bundled snapshot revision, original source URL, and snapshot retrieval date from clig-source.md; evaluated offline, not checked against the live site>
- **Audit date:** <Date of this audit, distinct from the snapshot retrieval date>
- **Method:** <source tracing, dependency inspection, runtime probes, existing tests>

### CLI map

| Entry point / command family | Parser / registration | Handler and shared behavior |
| --- | --- | --- |
| <command> | <file:symbol> | <output, errors, configuration, lifecycle locations> |

### Verification performed

| Check | Command or source path | Conditions | Result |
| --- | --- | --- | --- |
| <check> | <invocation or traced symbols> | <cwd, relevant env, TTY/pipe, fixtures> | <stdout/stderr/status or source conclusion> |

### Limitations

<Applicable areas not verified, reasons, and checks needed to resolve uncertainty. Distinguish tool/environment failures from application behavior. Do not list inapplicable CLIG items or count hypotheses as findings.>

## Findings

### <CLIG section or CLI aspect>

#### CLI-001 — <Title>

- **Severity:** <critical | major | minor | suggestion>
- **Type:** <discrepancy | improvement>
- **CLIG recommendation:** <Verified quotation or faithful paraphrase, source section link, applicable conditions. Identify any project-policy trade-off.>
- **Location:** <Repository-relative file:line-range and symbol; list affected commands and related locations when the cause is shared. If line numbers are unavailable, give the narrowest verified symbol/region.>
- **Current behavior:** <What happens now and under which inputs/environment.>
- **Evidence:** <Source trace or executed command/test. Label static versus runtime confirmation; include concise observed stdout, stderr, and CLI exit status when relevant.>
- **Issue:** <Concrete user/automation impact and severity rationale; explain why this is a discrepancy or an optional improvement.>
- **Recommended fix:** <Smallest change that addresses the root cause and its intended outcome.>
- **Implementation guidance:** <Specific existing integration point and verified library API or algorithm; relevant edge cases, regression checks, and interface/scripting impact. No applied patch.>

<Repeat for unique findings, grouped by theme. If none exist, replace this section's placeholders with an explicit statement that no confirmed findings were identified within the checked scope.>

## Summary

| Severity | Count |
| --- | ---: |
| critical | <count> |
| major | <count> |
| minor | <count> |
| suggestion | <count> |
| **Total** | **<count>** |

- **Classification:** <number of discrepancies and optional improvements>
- **Most significant issues:** <finding IDs and consequences, or none>
- **Overall alignment:** <Qualitative assessment bounded by verified coverage; acknowledge material uncertainty. No unsupported compliance percentage.>

## Recommended order of fixes

1. **<finding ID — title>** — <Why this comes first: data risk, usability, compatibility, scripting impact, or dependency on another fix.>
2. **<finding ID — title>** — <Rationale.>

<Reference every finding once. Place optional improvements separately at the end when appropriate. If there are no findings, state that no fixes are recommended based on this audit.>
