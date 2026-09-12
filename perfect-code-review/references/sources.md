# Sources and design rationale

Read this file when explaining, auditing, or updating the methodology, not during every code review. The links below point to the original publishers. The skill is a synthesis, not a claim that these sources prescribe its exact workflow or classification system.

## Primary engineering sources

### Google Engineering Practices

The complete [reviewer guide](https://google.github.io/eng-practices/review/reviewer/) was consulted, including all six sections:

- [The Standard of Code Review](https://google.github.io/eng-practices/review/reviewer/standard.html): improving overall code health rather than perfection; technical evidence over preference; documented style authority; accepting equally valid designs; resolving disagreements.
- [What to look for](https://google.github.io/eng-practices/review/reviewer/looking-for.html): design, functionality, complexity, tests, naming, comments, style, documentation, every relevant line, and system context. Tests must actually detect broken behavior; specialist review and explicitly scoped approval are legitimate.
- [Navigating a change](https://google.github.io/eng-practices/review/reviewer/navigate.html): understand the purpose, inspect the main implementation/design first, raise major problems early, then review remaining files logically.
- [Speed](https://google.github.io/eng-practices/review/reviewer/speed.html): prompt responses, early useful feedback, no unnecessary delay for optional comments, without sacrificing review quality.
- [Comments](https://google.github.io/eng-practices/review/reviewer/comments.html): respectful code-focused feedback, explain why, distinguish required changes from suggestions, balance corrective direction with author autonomy.
- [Handling pushback](https://google.github.io/eng-practices/review/reviewer/pushback.html): reconsider the author's evidence, withdraw when wrong, preserve justified code-health requirements, and resolve rather than prolong disagreement.

Also consulted [Small changes](https://google.github.io/eng-practices/review/developer/small-cls.html): conceptually focused and self-contained changes, related tests, separation of unrelated refactors, easier review and rollback; size is not just a line count.

### GitLab

[Code Review Guidelines](https://docs.gitlab.com/development/code_review/): understand necessity and author intent; review the chosen solution as well as overall code health; self-review; focused changes; architecture, tests, performance, reliability, observability, security, documentation, deployment and compatibility; involve domain experts; explicitly mark non-mandatory suggestions and keep progress moving.

The skill generalizes the reviewer/maintainer distinction into solution-level and system-level attention, without assuming AI authority to merge. GitLab-specific approver roles, labels, deployment constraints, line targets, and service-level objectives are not generic requirements. Compatibility is evaluated against the actual project's contracts and deployment policy, not GitLab's particular upgrade guarantees.

### Microsoft Engineering Fundamentals Playbook

- [Reviewer Guidance](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/reviewer-guidance/): functional and architectural reasoning, correct tests, logical reading order, context, scope discipline, considerate explanatory comments, a design pass followed by code-quality analysis.
- [Code Review Process Guidance](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/): focused changes, timely reviews, and automation of mechanical checks so reviewer attention can concentrate on functionality and design.

The skill does not turn heuristic complexity indicators (such as argument counts) into automatic defects. It uses questions for genuine uncertainty and direct factual statements for verified defects, rather than applying the guidance's preference for questions mechanically.

### OWASP

[Secure Code Review Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secure_Code_Review_Cheat_Sheet.html): distinguish baseline audits from diff-based review; prioritize changed controls, trust boundaries, new integrations and attack vectors; trace sources, transformations and sinks; inspect business-logic and authorization failures; validate automated leads; report concrete impact and evidence.

The skill uses this diff-based methodology, not its entire audit checklist. Algorithm choices and framework guarantees must be checked against relevant primary documentation for the actual use, rather than treating short generic security recommendations as universally sufficient. Its general evidence gate extends security data-flow reasoning to other behavioral defects.

## Feedback and priority models

### Conventional Comments

[Conventional Comments](https://conventionalcomments.org/) motivates explicit distinctions among issues, suggestions, questions, notes, and blocking/non-blocking intent. The site explicitly permits adapting the labels. The skill uses a smaller format and separate classification fields rather than the full convention. It does not adopt a praise quota; sincere optional feedback is compatible with a zero-finding review.

### Daniil Bastrich: PERFECT

[The PERFECT Code Review: How to Reduce Cognitive Load While Improving Quality](https://bastrich.tech/perfect-code-review/) motivates descending attention from Purpose and Edge Cases through Reliability, Form, Evidence, Clarity and Taste, with contextual application and personal taste last.

Deliberate adaptations:

- **Early design, risk-driven depth:** Google's navigation advice places major design analysis early. PERFECT remains an attention model, not an immutable phase order. A high-risk authorization change warrants immediate security analysis.
- **Separate reliability, security, performance and contracts:** PERFECT's Reliability groups performance/security concerns. The skill separates them because each needs different evidence and triggers, and explicitly covers failure handling and operational behavior.
- **Evidence throughout:** tests/CI support the whole review, not just PERFECT's Evidence phase. A passing suite does not prove a contract correct; a failed suite needs attribution, not automatic blame on the change.
- **Enforced invariants defeat speculation:** PERFECT recommends proactively resolving some currently “impossible” dangerous states. This skill requires a concrete introduced gap/violation before reporting fragility as a defect. Hypothetical future misuse creates excessive AI false positives.
- **Scoped approval is legitimate:** PERFECT criticizes bare LGTM and says reviewers are not responsible for the final outcome. Google/GitLab explicitly recognize reviewer responsibility and approval. The skill chooses an evidence-backed scoped recommendation, while retaining human/domain owners' merge responsibilities.

## Operational policies added for AI reviewers

The original engineering sources support context, evidence, clear feedback and reconsideration, but do not prescribe this exact AI protocol. The following are explicit synthesis/design decisions:

- A first-class adversarial verification pass: identify counterevidence, seek it, and discard candidates it defeats.
- A causal/reachability gate for behavioral defects and a separate requirement/burden gate for non-runtime issues.
- Independent severity, merge impact and confidence; suppression of speculative/minor uncertain findings; deduplication by root cause; no minimum finding count.
- Revision-aware scope tracking, truthful tool-result claims, review-content trust boundaries, and non-mutating/sandboxed execution safeguards.
- Three verdicts that distinguish established blockers from missing review evidence, without presenting an AI recommendation as a remote approval or proof of correctness.

## Trade-offs resolved

- **Tests:** Google expects tests with behavior changes (including coverage for refactors); Microsoft says adding them later is unacceptable. This skill preserves co-location and mandatory project policies but, for a portable precision-focused reviewer, does not universally block every test absence. A blocking test gap must establish meaningful unverified behavior/risk or cite an applicable mandatory rule. This is a deliberate relaxation of those organizations' defaults, not a quotation of them.
- **Progress vs. code health:** optional polish must not delay a useful change, but a demonstrated substantial defect is not excused by unrelated benefits or a vague follow-up promise. Concrete maintainability degradation remains reportable even without an immediate runtime failure.
- **Precision vs. recall:** suppressing low-confidence findings can miss real issues. Material unknowns are surfaced as targeted questions and an incomplete verdict; they are not inflated into defects or silently treated as safe.
- **Speed vs. verification:** communicate major blockers early only after counterchecking them, and disclose the incomplete scope. Avoid exhaustive unrelated audits and duplicated automation to recover review time.
- **Portability vs. invocation control:** the methodology is agent-neutral and intended for explicit user invocation. `disable-model-invocation: true` expresses that intent on hosts that support it; it is not part of the base Agent Skills specification. Configure manual-only invocation through the host's own controls where necessary.

## Updating this skill

Re-read the original linked sections before changing an attributed principle. Check qualifications and disagreements, not just headings or summaries. Keep new AI-specific policy labeled as synthesis. Do not require routine reviewers to fetch all these guides, and do not add a repository-specific rule without making its scope explicit.
