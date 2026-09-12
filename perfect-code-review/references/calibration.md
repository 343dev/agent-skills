# Finding calibration

Use these examples when the verification gate or classification is ambiguous. They illustrate decisions, not finding quotas or universal severities. Locations and implementation details are illustrative; use only real evidence in a review.

## 1. Counterevidence eliminates an apparent input bug

A changed handler indexes the first item without a local emptiness check. Before reporting a crash, inspect registration, callers, validation, and the collection type. Every reachable call passes through a validator requiring at least one item, and the change preserves that path.

**Decision:** discard the candidate. An absent local check does not establish a reachable failure. Do not retain a non-blocking “defensive programming” comment about imagined future callers. If the change adds an alternate entry point bypassing validation, trace that new path instead.

## 2. A passing test does not eliminate an authorization defect

A new invoice lookup uses a caller-controlled identifier after authentication. The query is globally scoped; route middleware checks login but not tenant ownership. Inspect the shared repository and response filtering: neither adds a tenant restriction. An ordinary authenticated user can request another tenant's invoice. Tests mock the lookup to return only the current tenant's data.

**Decision:** report one **High | blocking | confidence: high** finding for cross-tenant disclosure. Explain actor control, the absent ownership check, and the exposed information. Recommend enforcing ownership before returning data and a cross-tenant regression test. The misleading mock is supporting evidence, not a duplicate “missing security test” finding. No live attack is necessary to establish this path.

## 3. Separate severity from confidence

A change performs a table rewrite during deployment. The migration code and documented production topology support a path to an exclusive lock that blocks essential writes. Documentation establishes that traffic continues during migrations. The database's installed-version documentation confirms the lock mode, but production-duration measurements are unavailable.

**Decision:** a **High | blocking | confidence: medium** finding can be justified if table size/work estimates support a substantial write outage; state the unmeasured duration and the evidence needed to resolve it. If supported data volumes are unknown and nothing establishes a meaningful interruption, ask for deployment/size evidence and use **COMMENT**, rather than inventing an outage. A database keyword alone is not a performance finding.

## 4. Tests are risk evidence, not a file-count rule

A behavior-preserving internal rename has existing tests exercising its callers; no required policy calls for additional tests. No new test file appears.

**Decision:** no missing-test finding. Existing coverage may be sufficient.

Conversely, a change adds retry-after-timeout behavior to a payment operation, but tests cannot establish whether a request that succeeded remotely is retried safely. Inspect the provider's idempotency guarantee and request key lifecycle first. If retries are concretely non-idempotent, report the duplicate-charge defect and include the regression-test direction in that finding. If implementation correctness is supported but important failure behavior remains unverifiable, explain that precise test gap and its risk; a blocking test-gap finding needs more than “add edge cases.”

## 5. A convention is not a preference, and neither is automatically High

A repository explicitly requires a changelog entry for a public command rename. The change performs that rename but omits the entry; the rule is mandatory and applies. No automated check covers it.

**Decision:** a **Low | blocking | confidence: high** convention finding can be appropriate. Cite the rule and the release-information consequence. Do not call it personal taste or inflate its severity to justify blocking. If CI already reports this exact omission, summarize that required failure once under checks instead.

A preference for a different local variable name, without ambiguity or an applicable rule, is not an actionable defect. Omit it unless preferences were requested.

## 6. Pre-existing behavior needs a changed causal path

An old parser mishandles one input form. The change edits only nearby comments and never affects that path.

**Decision:** omit the parser bug from this review. If instead the change routes newly supported inputs through that parser, demonstrate the new call path and report the newly activated failure at the changed call site, with supporting parser context.

## 7. Approval vs. incomplete review

For a fully inspected bounded change, relevant test evidence and counterchecks support the contract and no candidate survives:

```text
Verdict: APPROVE
Scope: <actual base>..<actual head>; changed parser and its callers/tests.
No actionable findings.
Checks: <actual targeted test command and result>.
```

For a patch with a changed authorization call but unavailable middleware and no way to establish the enforced policy:

```text
Verdict: COMMENT
Scope: supplied patch; shared middleware unavailable.
No actionable findings.
Checks: Static inspection only; runnable project not supplied.
Limitations / questions: Does the shared middleware enforce resource ownership
for this route? Its implementation or contract is needed to assess access safety.
```

The second result is not an approval and does not allege an authorization bypass. Avoid burying material missing evidence under an otherwise confident verdict.
