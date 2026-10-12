---
name: user-scenario-design
disable-model-invocation: true
description: >-
  Create, discover, organize, and audit user scenarios for interactive digital
  products. Use when creating or rewriting scenarios, building functional
  hierarchies of user tasks, reviewing user flows or UX documents from a
  scenario-design perspective, or reorganizing scenario sets. Identify missing,
  duplicated, oversized, overly fragmented, and UI-dependent scenarios. Separate
  user goals from interface implementation and distinguish scenarios,
  subscenarios, and variants.
---

# User Scenario Design

## Working contract

Describe real user situations and tasks, not sequences of screens or controls.
Do not use this skill merely to design screens, navigation, interaction details, or visual UI unless the request also involves identifying or evaluating the underlying user scenarios.
Apply this method to websites, web, mobile, and desktop applications, SaaS, enterprise systems, digital services, kiosks, and terminals.
Work with existing products, partially built products, and products without an interface.
Use the supplied material and these rules. No external methodology sources or web access are required.

Use the user's language for questions, analysis, hierarchies, audits, and scenarios unless the user requests another language.
Translate template headings accordingly. Preserve established domain terms, product names, and commands in their natural contextual form.
Keep the work practical. Explain methodology only when it clarifies a correction or decision.
Classify the material yourself rather than asking the user to classify UX terminology.

Infer the mode:
- **Create:** Discover, create, expand, organize, or map user tasks and scenarios.
- **Audit:** Review supplied scenarios, flows, hierarchies, UX documents, or mixed product requirements.
- **Mixed:** Audit existing material, identify structural gaps, then create supported additions or propose hypotheses.

Start directly when context is sufficient. For an audit, inspect the supplied material before asking about the product.

## Task model

Capture the relevant role, trigger, motivation, knowledge, starting resources, goal, domain actions, decisions, constraints, result, and significant variants.
Include only details that affect the task.

Apply this central test:

> If the interface were completely redesigned while the user's underlying task remained unchanged, would the scenario still make sense?

If not, recover the domain meaning behind the interface actions. Do not merely delete those actions or substitute vague verbs.

| Interface-level statement | Task-level formulation |
| --- | --- |
| Enter the artist name in the Search field | Find music by an artist the user already knows |
| Click Download | Obtain the selected files for the task the user needs to complete |
| Open the dashboard | Check whether any project needs attention, if that purpose is supported |
| Click Save | Save the changes |

Keep enough specificity to identify what the person evaluates, chooses, approves, obtains, transfers, or decides.
If several purposes fit an interface action, mark the purpose as uncertain or ask a consequential question.
Retain an interface object only when that object is genuinely the task's subject, such as configuring a keyboard shortcut.

Name each scenario after a meaningful task or outcome: **Find a known release**, **Resolve a failed payment**, **Approve a publication**.
Replace names such as **Search page**, **Checkout flow**, or **Settings tab** with supported task names.

Use roles, not decorative personas. Include knowledge, experience, authority, permissions, and context only when they affect the task.
For example: “An editor authorized to publish content” or “A DJ preparing material for an upcoming performance.”
Include demographic or biographical details only when evidence shows that they change the scenario.

Screens, clicks, component states, loading, animations, redirects, validation, notifications, APIs, HTTP statuses, and database operations are not scenarios by themselves.
Preserve relevant implementation requirements separately under **Outside the user scenario**. They are not useless because they are outside this model.
A notification can trigger a task. A technical failure can affect a task. Neither automatically defines a separate scenario.

## Evidence and minimum questioning

Keep these evidence levels distinct where uncertainty affects the result:
- **Fact:** Explicitly stated by the user or supplied material. Attribute source claims rather than treating them as verified research.
- **Inference:** Strongly supported by available information, but not explicit. Label the inference when using it.
- **Hypothesis:** Plausible but unconfirmed. Present it conditionally or as an option for validation.

Never invent motivations, pain points, preferences, constraints, demographics, usage patterns, frequency, business importance, conversion problems, research findings, or organizational rules.
Do not turn a proposed scenario into a product requirement without confirmation.
Use a compact assumptions note when useful instead of attaching a label to every sentence.

Before each question:
1. Reuse everything already supplied. Extract usable context from examples and current flows.
2. Identify the unresolved point with the greatest effect on the result.
3. Compare plausible answers. Ask only if they materially change a boundary, role, goal, starting information, sequence, decision, constraint, result, or variant classification.
4. If strong evidence supports an answer, proceed with a labeled inference when appropriate.
5. If uncertainty prevents a reliable choice, ask the smallest useful question. Otherwise produce a partial result with the uncertainty visible.

Prefer one question that resolves an important choice. Group 2–4 tightly related questions only when doing so reduces unnecessary turns.
Offer plausible options when the user cannot answer, without asserting that any option is true.
Do not repeat every answer, ask for known information, or interview merely to populate a template.

When almost no context exists, ask only for missing scope information:
“What is the product, who uses it, and are we working on one task or the product as a whole?”
For an unclear interface-level goal, ask about its purpose only when the answer cannot be inferred and would change the scenario.

**Stop interviewing** when the role, trigger, useful goal, and completion condition support a coherent sequence, with consequential uncertainty resolved or explicit.
Produce a useful partial result sooner if remaining unknowns block only part of the work.
Ask further questions only for unresolved choices that materially affect that result.

## Hierarchy and boundaries

Use these distinctions:

| Level | Decision rule |
| --- | --- |
| Broad user task | Groups related goals, such as “Manage purchases.” It is not automatically one scenario. |
| Scenario | Describes a coherent situation and task with a meaningful goal and observable completion. |
| Subscenario | Describes a coherent nested task that deserves separate detail. It can be reused by multiple parent scenarios. |
| Variant | Describes a limited difference within a scenario while preserving most of its role, motivation, goal, principal sequence, and result. |

Prefer a variant when only a limited portion changes.
Consider a separate scenario when role, motivation, starting situation, goal, resources, decision criteria, sequence, or result changes materially.
Judge the effect of the difference, not the number of differing fields.
Different knowledge or permissions can justify separation. Different role names alone do not.

A scenario may involve multiple actors when they participate in the same end-to-end situation.
Keep the primary user's goal explicit and identify handoffs rather than splitting solely because another actor becomes involved.

Extract a subscenario when a nested task has its own meaningful result and needs separate detail.
Reference it from parent scenarios rather than duplicating its full sequence. Keep the parent's overall result explicit.
Split independent goals even when the current interface puts them in one flow.
Merge duplicates and minor branches when their underlying task is the same.
Never split solely by screens, individual actions, technical branches, or component states.

Example:
- Scenario: Obtain music for an upcoming performance.
- Variant: The DJ knows the release rather than only the artist.
- Subscenario: Evaluate whether a track fits the intended set.

“Find product → purchase → track shipment → return → contact support” likely combines independent situations and outcomes.
“Server returns HTTP 500” is a technical detail.
“Obtain a required document another way before a deadline” can be a recovery task if the context supports it.

## Create procedure

### 1. Establish scope

Determine the product, relevant roles, real tasks, product maturity, and requested breadth from available context.
For several roles, identify each role's main tasks before choosing scenarios for detailed work.
Present plausible roles as hypotheses when they are not confirmed.

For new products, establish tasks before discussing pages, navigation, controls, or layouts.
For existing or partially built products, use the interface as evidence, not as the authoritative task model.
Distinguish **the underlying task** from **its current implementation**.

### 2. Map tasks when the scope is broad

For one concrete scenario, proceed directly to step 3.
For a whole product or a large area, first create a functional hierarchy of user tasks, not a sitemap.
Keep sibling tasks at comparable abstraction levels. Stop decomposition before choosing interface solutions.

```text
Music service
├── Find music
│   ├── Find a known release
│   ├── Find music by a known artist
│   └── Explore available music
├── Evaluate music
│   ├── Assess suitability for a purpose
│   └── Compare alternatives
└── Obtain music
    ├── Obtain a complete release
    └── Obtain an individual track
```

Use the hierarchy to identify gaps, duplicates, oversized tasks, excessive fragmentation, and reusable subscenarios.
Treat its leaves as candidate tasks, not automatically as independent scenarios. Apply the hierarchy rules before expansion.
Mark uncertain additions as hypotheses.

Choose the next action:
- Task map only requested: return the hierarchy and stop.
- Priorities known: expand the highest-priority scenarios.
- One branch clearly central to the request: expand it and state the scope assumption.
- Priorities unknown and branch choice materially affects the work: ask which branch to expand.
- Complete coverage explicitly requested: cover all in-scope branches systematically. Keep secondary detail proportionate and identify unresolved coverage.

Use labels such as core, frequent, critical, risky, secondary, or future only with supporting evidence or explicit prioritization.
Do not immediately expand dozens of scenarios merely because the map contains many tasks.
Keep **unsupported scenario** separate from **supported task without dedicated UI yet**. The former changes product scope, the latter implementation.

### 3. Establish the situation and starting information

Identify the relevant role and what happens before the person turns to the product.
Determine why the task arises now and what the person already knows, has, or can access.
Starting information can include names, identifiers, documents, files, links, criteria, budgets, permissions, and previous interactions.
Identify its source when that source affects the task.

Include physical context, accessibility needs, time pressure, connectivity, or work across systems only when they affect actions or decisions.
For example, warehouse work or one-handed use can constrain a task. Do not add context for narrative flavor.
Resolve only consequential unknowns through the questioning rules.

### 4. Define the goal and successful result

Express a useful outcome, optionally with its larger purpose:
“The user wants to [meaningful result] so that [larger task can be completed].”
Define success by asking internally: “How does this person know the task is complete?”
Use observable results such as files obtained, a decision recorded, a meeting scheduled, or work approved.
A defensible decision not to proceed can be successful when the goal is evaluation. “The user is satisfied” is insufficient.

### 5. Reconstruct the natural sequence

Write ordered domain actions, decisions, and relevant events from the starting situation to the result.
Include the criteria and consequences of important choices when known.
Examples include whether an option fits the need, whether information is sufficient, whether consultation is required, and what deserves attention.
Keep actors explicit during handoffs. Distinguish system events from user actions.
Remove logical gaps, repetition, and unexplained changes in goal or role.

For example: determine needed material → find a relevant release → evaluate suitability → compare alternatives if needed → choose tracks → obtain files.
Keep purpose and decision criteria concrete without introducing unsupported domain rules.

### 6. Classify differences and finalize

Check differences in starting information, experience, triggers, criteria, constraints, and recurring nested tasks.
Classify each as a variant, subscenario, or separate scenario using the hierarchy rules.
Include failures only at the level where they change the user's task, decision, or result.
Apply the final quality check before returning the requested scope.

## Audit procedure

### 1. Inspect and choose depth

Judge the content, not its title.
Internally classify it as a scenario, user flow, screen list, functional requirements, use case, technical specification, task hierarchy, sitemap, or mixture.
A valid user flow is not defective merely because it is not a scenario. Identify what must change for scenario-design purposes.

- **Quick audit:** For “quickly check,” “what is wrong,” or “is this okay,” give a concise verdict and roughly 3–5 highest-impact findings.
  Report fewer when fewer exist. Include a corrected version when the evidence permits.
- **Deep audit:** For a full document, scenario system, or product audit, examine the whole supplied scope using the checks below.
  State coverage limits if the available material cannot support a product-wide conclusion.

### 2. Check the scenario and the set

| Area | Check and correction |
| --- | --- |
| Situation | Recover the real trigger before “opens the app.” Identify relevant context and starting resources. |
| Role | Remove decorative details. Separate materially different authority, knowledge, goals, constraints, decisions, or success criteria. |
| Goal and name | Replace means such as “use search” or “complete form” with the supported useful outcome. |
| Interface independence | Recover domain meaning behind clicks, fields, filters, tabs, pages, and popups. Preserve relevant implementation requirements separately. |
| Sequence | Find gaps, missing decisions, repetition, excessively broad steps, excessive detail, actor confusion, and unexplained goal changes. |
| Result | Identify what observably changes. Repair an absent or vague completion point. |
| Boundaries | Split independent motivations and outcomes. Merge unnecessary fragments. Extract coherent nested tasks where warranted. |
| Variants and duplication | Compare underlying tasks, not titles. Merge equivalent scenarios, keep limited differences as variants, and expose independent tasks hidden as tiny branches. |
| Set structure | Check important roles and tasks, abstraction levels, terminology, uneven detail, hierarchy versus sitemap, dependencies, and supported priorities. |

For gaps, distinguish evidence of an omission from a candidate requiring validation.
For example: “Repeat purchase may be missing if returning customers need to reorder.” Do not invent that behavior.
Check prerequisites and handoffs without converting every dependency into a separate scenario.

### 3. Prioritize corrections

Order findings by their effect on usefulness:
1. **Critical:** Wrong task, incompatible goals, no meaningful user goal, or material otherwise unusable as a scenario.
2. **Structural:** Faulty boundaries, roles, situations, goals, hierarchy, abstraction, decisions, results, or variant classification.
3. **Clarity:** Wording or missing context that reduces precision without changing the underlying task.

Group related findings. Fix major structural problems before minor wording.
For each finding, quote or point to the relevant fragment when available, explain the effect, and give a concrete correction.
When reviewing structured source material with stable section names, line numbers, IDs, or other locations, preserve those references when useful.
Never invent source locations.
For missing content, identify the affected area instead of inventing a quotation.
Show severity labels only when they help the user act.

### 4. Reconstruct the useful artifact

When evidence is sufficient, return the complete revised scenario rather than isolated edits alone.
For a set, return the corrected hierarchy and identify retained, merged, split, extracted, and proposed items as applicable.
Keep source names or identifiers traceable when reorganizing supplied material.
For a map-only request, stop at the corrected map. For complete-set requests, revise all in-scope scenarios at appropriate depth.
Preserve relevant interface and system requirements outside the scenarios.

When evidence is insufficient, return supported findings and a useful partial revision with explicit unknowns or assumptions.
Ask only the minimum questions needed for consequential unresolved choices. Do not replace the audit with a generic product questionnaire.

## Output templates

Adapt these defaults to the requested scope and language. Omit sections that add no information.
Represent decisions and constraints where they affect the situation or sequence, rather than adding empty fields.

### Full scenario

```markdown
# [Task-oriented scenario name]

## Role
[Relevant knowledge, authority, experience, and constraints.]

## Situation
[Real-world trigger and why the task arises.]

## Starting information
[What the person already knows, possesses, or can access.]

## Goal
[Useful outcome and its purpose when known.]

## Scenario
1. [Domain action or relevant event.]
2. [Decision with known criteria and consequences.]
3. [Continue to completion.]

## Result
[Observable successful completion.]

## Variants and subscenarios
- Variant — [Condition, changed portion, and resulting difference.]
- Subscenario — [Nested task, when it is needed, and its result or reference.]
```

When needed, append **Assumptions and open questions** or **Outside the user scenario**.
Use the latter for preserved screen structure, controls, loading states, error copy, validation, APIs, or visual and technical requirements.

### Functional hierarchy

Return a task tree grouped by broad task or relevant role, followed by only consequential notes:
- Candidate variants, shared subscenarios, and dependencies.
- Supported priorities and scope exclusions.
- Hypotheses or gaps needing confirmation.
- The branch selected for expansion, or the one question needed to select it.

### Audit

```markdown
## Verdict
[Compact assessment and scope limits when relevant.]

## What works
[Meaningful strengths, if present.]

## Problems

### 1. [Highest-impact problem]
**Original**
> [Relevant fragment when available. For a gap, identify the affected area. Preserve useful existing references.]

**Problem**
[Practical or structural effect.]

**Better**
> [Concrete correction, conditional when evidence is incomplete.]

## Revised scenario
[Complete revision when supported, or an explicitly partial version.]
```

For sets, replace **Revised scenario** with **Revised scenario set** and include the corrected hierarchy and structural changes.
Add only consequential assumptions or questions. Do not generate microscopic comments to fill the template.

## Final quality check

Silently check the requested artifact before returning it:
- Task-oriented names, relevant roles, real triggers, and clear starting information.
- Meaningful goals, explicit important decisions, logical sequences, and observable successful results.
- Domain actions instead of unnecessary interface mechanics, without losing task specificity.
- Implementation requirements preserved separately when relevant.
- Independent tasks separated, minor branches not fragmented, variants and subscenarios correctly classified.
- Consistent hierarchy, terminology, dependencies, and evidence-supported priorities across a set.
- Facts distinguished from consequential inferences, hypotheses, and unknowns.
- Interface-redesign test passed, requested scope addressed, and user language respected.

Correct significant failures. If evidence prevents correction, expose the limitation rather than inventing the missing information.
