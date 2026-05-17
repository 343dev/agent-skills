---
name: perfect-code-review
description: >
  Structured code review using Daniil Bastrich's PERFECT principles: Purpose,
  Edge Cases, Reliability, Form, Evidence, Clarity, and Taste. Use this skill
  for any code review task: PR review, patch review, diff review, merge
  readiness, safe-to-approve checks, review comments, security review,
  performance review, test review, architecture review, self-review checklist,
  code review policy, or separating blockers from nits/taste. Trigger when the
  user says review this PR/diff/patch, asks if code is safe to merge or approve,
  mentions LGTM, bikeshedding, PERFECT, Purpose/Edge Cases/Reliability/Form/
  Evidence/Clarity/Taste, or wants prioritized constructive findings instead of
  broad style feedback.
---

# PERFECT Code Review

Use this skill to review code with a clear priority order. The goal is to reduce reviewer cognitive load while increasing review value: find issues that matter, explain why they matter, and avoid blocking changes on personal taste.

PERFECT priority order:

1. Purpose: code solves intended task.
2. Edge Cases: business and technical corner cases are handled.
3. Reliability: performance, security, data safety, and integration risks are acceptable.
4. Form: design aligns with project principles and keeps cohesion high, coupling low.
5. Evidence: tests, CI, builds, migrations, and contracts support the change.
6. Clarity: code communicates intent and fits team conventions.
7. Taste: personal preferences are non-blocking unless supported by clear reasoning.

## Review Workflow

1. Establish context before judging code.
   - Identify task goal from PR description, ticket, commit message, user prompt, changed files, or tests.
   - If goal is unclear and affects correctness, ask one focused question or state the assumption before reviewing.
   - Compare implementation against expected behavior, not against personal preferred implementation.

2. Inspect changed code in PERFECT order.
   - Start with business correctness and missed requirements.
   - Then look for edge cases: empty input, missing data, null/undefined, duplicate input, limits, time zones, concurrency, partial failure, retries, permissions, and impossible states that are only safe because of fragile assumptions.
   - Then examine reliability: security boundaries, authorization, input/output validation, secrets, unsafe file paths, injection, resource use, N+1 queries, memory pressure, race conditions, cache invalidation, broken integrations, and backward compatibility where externally visible behavior exists.
   - Then review form: ownership boundaries, cohesion/coupling, unnecessary abstraction, duplicated logic with real maintenance cost, and fit with existing architecture.
   - Then review evidence: tests, CI/build, type checks, migrations, generated files, contract changes, observability, and manual verification notes.
   - Then clarity: naming, structure, comments, error messages, and whether intent is readable without stepping through every line.
   - Treat taste as optional feedback. Mark it explicitly as non-blocking.

3. Write comments as actionable findings.
   - Explain what is wrong, why it matters, and one practical fix direction.
   - Include file and line references when available.
   - Avoid vague comments like `this is bad`, `clean this up`, or bare `LGTM`.
   - Do not inflate severity for subjective preferences.

## Severity

Use severity to show merge risk:

- Blocker: likely incorrect task outcome, serious production risk, security issue, data loss, broken API/contract, failing required checks, or missing essential evidence.
- Major: real bug or maintainability risk that should normally be fixed before merge, but has contained blast radius.
- Minor: small correctness, reliability, evidence, or clarity issue worth fixing but not merge-blocking alone.
- Nit: taste or small readability preference. Non-blocking by default.

When uncertain, choose the lower severity and describe the assumption that would make it worse.

## Output Format

Lead with findings. Keep summaries brief. If no findings, say so and list residual risks or unverified checks.

Use this structure:

```markdown
**Findings**
- [Severity] [PERFECT category] `path:line` Problem. Impact. Suggested fix.

**Open Questions**
- Question or assumption that affects review confidence.

**Verification**
- Checks reviewed or run.
- Checks not run or unavailable.

**Summary**
Short merge-readiness assessment.
```

Omit empty sections except `Findings`; if there are no findings, write `No findings.` under it.

## Comment Quality Checklist

Before returning a review, verify each finding passes this checklist:

- It maps to one PERFECT category.
- It describes concrete risk, not only personal dislike.
- It explains why the risk matters for users, maintainers, security, performance, or delivery.
- It gives a fix direction without requiring one exact implementation unless only one safe implementation exists.
- It does not duplicate an automated formatter/linter concern unless automation is missing or failing.

## Review Modes

Default to full review when user says `review`, `code review`, `PR review`, or asks if code is safe to merge.

If user asks for a narrow review, stay within scope but still mention severe out-of-scope risks if noticed:

- `security review`: emphasize Reliability, then Purpose and Evidence.
- `performance review`: emphasize Reliability and Edge Cases.
- `test review`: emphasize Evidence, Purpose, Edge Cases, and test clarity.
- `architecture review`: emphasize Form, Purpose, and Reliability.
- `self-review checklist`: produce a concise checklist in PERFECT order instead of findings.

## Self-Review Checklist

When asked to prepare a checklist before submitting code, use:

```markdown
**PERFECT Self-Review**
- Purpose: What task does this solve? Which behavior changed?
- Edge Cases: Which boundaries, empty states, invalid inputs, and impossible states did I check?
- Reliability: Any security, performance, data integrity, concurrency, or integration risk?
- Form: Does this fit existing ownership boundaries with high cohesion and low coupling?
- Evidence: Which tests, type checks, builds, migrations, or manual checks prove it works?
- Clarity: Can a teammate understand intent from names, structure, and errors?
- Taste: Which preferences are optional and should not block review?
```

## Style

Be direct, specific, and constructive. Prefer fewer high-signal findings over long lists of low-value comments. The reviewer earns trust by catching important issues and by not turning personal preference into policy.
