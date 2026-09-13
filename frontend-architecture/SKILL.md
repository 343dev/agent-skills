---
name: frontend-architecture
description: Explicitly invoke to design or review frontend architecture, assess risks, decide boundaries and state ownership, compare approaches, or plan an architectural refactor.
disable-model-invocation: true
---

# Frontend Architecture

Act as an architecture decision system: manage the cost of change, not the number of abstractions.

## Working approach

Choose **design** for new architecture or approach comparison; **review** to evaluate an existing implementation or design. Before designing a refactor, reconstruct the affected architecture. Scale depth to the decision, not automatically to the whole application. Keep analysis non-mutating unless implementation is requested.

In an existing codebase, treat established project conventions and boundaries as constraints unless there is evidence they are causing material architectural harm. Prefer an adequate solution in the project's existing idiom over introducing a second architectural dialect.

Follow this direction, revisiting earlier decisions when later evidence contradicts them:

```text
problem → constraints → expected lifetime and change profile
→ expensive-to-reverse decisions → quality attributes
→ responsibilities → ownership → boundaries and contracts
→ dependency topology → data and lifecycle ownership
→ communication topology → plausible variation
→ simplest adequate mechanism → technology/framework mapping
→ trade-offs and consequences
```

Use YAGNI, KISS, DRY, SOLID, GRASP, and patterns as diagnostic questions, not compliance rules. Apply the decision lenses below where relevant, not as a mandatory list of findings.

## Design workflow

1. **Frame the actual problem.** Extract the business goal, required scenarios, constraints, and non-goals from the user's material and repository evidence. Establish expected lifetime, change frequency, team size and capability, execution environments, time-to-market, cost of failure, expected reuse, and replacement cost. Surface costly-to-retrofit inputs where applicable: accessibility, offline behavior, indexability/SSR, routing, shared state, testing model, and browser/runtime compatibility.
   - Separate facts, assumptions, and unknowns. For each material unknown, name the decision it affects and ask only a question that could change that decision. If interaction is unavailable, use the smallest explicit assumption needed or give conditional alternatives. Do not invent business requirements or block on low-impact unknowns.
   - **Result:** a scoped problem and an economic horizon for architectural investment, not an assumption of indefinite evolution.

2. **Identify expensive choices and desired qualities.** Identify decisions with substantial reversal cost, blast radius, migration effort, or cross-system consequences. These are architectural; folder structure alone is not. Prioritize relevant qualities: readability, maintainability, extensibility/flexibility, testability, reliability, portability/reusability, performance, cohesion, and controlled coupling. Use actual change scenarios rather than invented numerical targets.
   - **Result:** consequential decisions and quality priorities, separated from cheap local choices.

3. **Assign responsibilities and ownership.** Apply the responsibility lens: assign knowledge/invariants, scenario orchestration, and dependency selection/construction/wiring to cohesive owners.
   - **Result:** each unit has a semantic purpose at a stated abstraction level, with independent axes of change considered.

4. **Define boundaries and dependency topology together.** Specify participants, inputs/outputs, contract owner, invariants, failure behavior, and lifecycle/ordering requirements. Distinguish runtime communication arrows from code-dependency arrows. Apply the dependency lens to expose hidden coupling and justify direction.
   - **Result:** consumers can use important contracts without reading implementations.

5. **Resolve data, lifecycle, and communication.** Apply those lenses, including routing/rendering where relevant. Trace representative success, failure, navigation/disposal, and stale-async-result paths.
   - **Result:** state has a source of truth; resources have release owners; interactions have a deliberate topology.

6. **Choose mechanisms around plausible change.** Apply the variation lens to evidence-backed change within the product's realistic direction. Start with a local conditional, function, configuration, or direct collaboration. Consult the secondary mechanism map only after understanding the forces.
   - **Result:** the simplest adequate mechanism, with its abstraction cost justified and speculative functionality left unimplemented.

7. **Map to technology and assess consequences.** Map responsibilities onto the existing or selected framework's idioms. Evaluate team capability, application/integration model, opinionatedness, ecosystem responsibility, runtime constraints, architecture freedom, and replacement cost. Explain which infrastructure the framework handles and what a custom alternative returns to the team. Compare meaningful alternatives, including retaining the current approach.
   - **Result:** a recommendation satisfying the output requirements below, with contract-level verification defined before implementation tasks.

## Architecture-review workflow

1. **Establish scope and context.** Identify the target, intended behavior, relevant constraints, and available evidence. Read applicable instructions, design records, manifests, entry points, relevant implementations, consumers, and tests. Label uninspected areas and uncertain intent; do not infer the architecture from directory names alone.
2. **Reconstruct the system.** Trace representative scenarios from user intent through orchestration, data/infrastructure, state-to-view propagation, and teardown. Build a compact map of actual responsibilities, owners, contracts, and dependency directions. Use definitions/references where available to verify relationships. For design-only input, distinguish specified guarantees from untested intentions.
3. **Find change amplification.** Trace what one plausible rule, UI, infrastructure, or environment change would touch. Apply the responsibility, dependency, and abstraction lenses to find duplicated knowledge, boundary leaks, scattered policy, hidden ordering, and unnecessary cognitive load.
4. **Check ownership and containment.** Apply the data, lifecycle, communication, and rendering lenses. Evaluate test coupling. Separate architectural from local code quality: ugly internals behind a stable contract may be replaceable debt; elegant functions with a wide dependency blast radius may be an architecture problem.
5. **Verify and prioritize recommendations.** Treat smells as leads, not proof. Check callers, contracts, framework guarantees, tests, and documented trade-offs for counterevidence. For each retained issue, connect evidence to a concrete change cost or failure consequence. Compare the smallest correction with broader alternatives, including leaving contained debt in place. Prioritize by impact, likelihood of relevant change/failure, blast radius, and remediation cost; distinguish confidence from impact.
6. **Deliver a bounded judgment.** Use the review output requirements below. For a refactor, propose incremental boundary changes with verification and migration consequences, not a default rewrite.

**Review complete when:** the agreed scope is accounted for, retained findings have supporting evidence and counterchecks, and proposed changes explain their benefit and cost. Missing evidence is a limitation, not proof of a defect.

## Decision lenses

### Change cost and economics

- What becomes expensive if this decision changes? How large is the blast radius? Can it be localized?
- Will the expected lifetime and change rate justify the initial investment? A disposable prototype and a long-lived product need different investments.
- What benefit does debt buy now, what future cost does it create, and can a stable boundary contain it? Make accepted debt visible with an owner and repayment trigger or strategy; debt is not automatically failure.
- Use complexity, coverage, and dependency counts as indicators, not quality verdicts.

### Responsibility, cohesion, and knowledge

- What concept does this unit own, at what abstraction level? Are its internals cohesive around that purpose? Which independent changes force it to change?
- Treat SRP as deliberate semantic boundary design, not one method, one action, or a mechanical count of reasons to change. Co-located markup, behavior, and styles may implement one cohesive responsibility; separation by file type proves nothing.
- Use **Information Expert** to place behavior near required information and invariants, unless it belongs to an independently changing responsibility. Use **Controller/use-case orchestration** for scenarios spanning several owners; **Creator** for significant construction/wiring; **Indirection/Pure Fabrication** for technical roles with concrete benefits. These are roles, not required classes or folders.
- Apply DRY to duplicated knowledge, responsibility, and business rules. Would one rule change necessarily require both locations to change? Similar syntax can represent different concepts; different syntax can duplicate the same rule. Prefer independent code over a false shared abstraction.

### Dependencies and contracts

- What does the unit depend on? Which dependencies are intrinsic collaboration and which are incidental infrastructure? Does the consumer unnecessarily choose a concrete implementation?
- Include imports, globals, browser/runtime APIs, framework assumptions, initialization order, required call order, and implicit lifecycle phases. Make invalid sequences explicit in the contract or impossible through the API where worthwhile. Controlled coupling, not zero coupling, is the goal.
- Treat the boundary and its connection as one decision. What crosses it, in which direction, under whose contract and invariants? What happens on failure? Does use require knowledge of hidden implementation details?
- Distinguish **DIP**, the architectural principle, from **DI**, supplying dependencies from outside, and a **DI container**, optional wiring automation. Where infrastructure should not define application policy, use an application-owned contract that infrastructure satisfies. Direct dependencies inside a cohesive subsystem can be correct; do not invert or inject everything.
- Use the Law of Demeter to investigate knowledge of a collaborator's internal collaborators, not to count dots. Consider whether behavior belongs behind the collaborator's contract or in a different owner.
- Treat browser globals and runtime-specific APIs as dependencies. SSR or an alternative runtime can stress-test a boundary without becoming a new product requirement.

### Data and lifecycle ownership

For relevant state, determine **owner, readers, writers, scope, lifetime**, and whether it must survive page/component destruction. Then distinguish:

| Distinction | Decision |
| --- | --- |
| Source vs. derived | Derive deterministic values by default. If independently stored for a concrete performance/lifecycle reason, specify synchronization/invalidation ownership and failure behavior. |
| Domain/model vs. view/presentation | Would the value or rule exist with a different UI? Keep representation-only concerns with their view owner; physical file separation is optional. |
| External/uncontrolled vs. validated/trusted | Identify where parsing, validation, and normalization establish guaranteed invariants. Let consumers rely on that contract rather than duplicating defensive checks everywhere. |

Treat lifecycle as resource ownership. For every acquired external resource, identify **creator, lifetime owner, release owner, and release condition**. Trace normal completion, failure, and disposal where applicable:

```text
subscribe → unsubscribe
register listener → remove/abort
start request → cancel or ignore obsolete results
mount → unmount/teardown
retain resource/reference → release
```

Make subscription cleanup explicit and easy, for example by returning a release capability. If uniqueness is needed, define “one per which scope and lifetime,” including cleanup; do not assume an application-global singleton.

### Communication and rendering

Ask first: **Is this shared state, notification, or coordination? Would direct collaboration be simpler?**

```text
persistent/shared data → state mechanism
one producer, independent consumers → observer/event mechanism
several participants coordinating a scenario → mediator/coordinator
```

These mechanisms can coexist. Do not turn transient events into global state or default to a global event bus when a direct relationship is clearer.

When relevant, distinguish:

- **Router:** URL/history, route matching, navigation, and what should be active.
- **Renderer:** how active content is instantiated/rendered and how state changes reach the view.
- **Page/component lifecycle:** resource creation, update, and teardown.
- **Loading boundaries:** how module/page boundaries and the chosen tooling affect code splitting and loading behavior.

A framework may intentionally combine these implementations; separate the responsibilities conceptually without forcing extra layers.

### Variation and abstraction

- What is actually expected to vary, and what evidence makes it plausible? YAGNI rejects speculative functionality, not a small justified seam. Open/Closed supports such seams without pre-implementing hypothetical variants.
- Is a function or configuration enough? Does a named pattern clarify a useful mechanism or just add ceremony? Simplicity is cognitive load, not minimum lines or maximum decomposition.
- What complexity does this abstraction remove, and what concepts/indirection does it add? Keep it only when the net cognitive load or another explicit quality trade-off justifies it.
- Growing flags that switch substantial behavior may hide scenarios, strategies, or responsibilities. Backward compatibility is a constraint, not evidence of extensibility. Determine whether compatibility is required by existing consumers, contracts, migration cost, or product constraints instead of preserving it automatically.
- Prefer composition and delegation without subtype semantics. Before inheritance, require a semantic “is-a” relationship and a need for polymorphic substitution consistent with the contract. Inheritance solely for reuse is insufficient; mixin-heavy designs merit reconsidering explicit collaborators.
- Keep universal base abstractions limited to genuinely universal capabilities. Compose or inject optional routing, state, analytics, and similar capabilities separately rather than making every consumer depend on them.

### Technology and current facts

- Why does this framework/library fit the established constraints? What lock-in, vendor-specific data/configuration, and replacement cost does it introduce? Is mitigation worth the additional complexity?
- Compare opinionatedness as a trade-off: framework conventions can reduce decisions and improve consistency; flexibility transfers more architectural responsibility to the team. Do not universally rank frameworks.
- Only browse when the current fact could materially change the architectural decision. For such framework, browser API, runtime, build-tool, or ecosystem claims, verify authoritative documentation against the relevant version and support targets, and cite it. If verification is unavailable, label uncertainty and avoid making the claim decisive.
- Keep stable concepts separate from version-specific facts. Do not treat historical framework internals, SSR/SEO absolutes, rendering-performance slogans, or dated API availability as permanent rules.

## Secondary mechanism map

Consult only after establishing **problem → forces → desired quality → simplest fitting mechanism**. Pattern names are optional shared vocabulary, not a selection target. Prefer idiomatic language/framework mechanisms over textbook class diagrams.

| Established problem shape | Candidate mechanism |
| --- | --- |
| Stable workflow with an independently variable behavioral step | Injected function / Strategy |
| Incompatible provider and consumer interfaces | Adapter; change interface compatibility |
| Subsystem too complex for its consumers | Facade; simplify the interaction level |
| Optional, stackable behavior under a compatible contract | Wrapper / Decorator with delegation |
| One producer notifying independent consumers | Subscription / Observer |
| Participants needing coordinated behavior | Coordinator / Mediator |
| Request passed among independent handlers | Handler sequence / Chain of Responsibility; specify ordering and termination |
| Complex assembly/configuration | Builder, only when construction merits its own responsibility |
| Deferred or repeated construction | Factory; inject an instance instead when it is needed now |
| Sequence of data transformations | Function composition / pipeline, weighing readability against measured performance needs |

## Overarchitecture guards

- Do not invent abstractions for hypothetical requirements or add repositories, services, controllers, factories, or layers because their names sound architectural. Tie every introduced role to a current responsibility or justified seam.
- Do not wrap every third-party library or inject every dependency. Use direct cohesive collaboration unless a material risk or policy boundary justifies indirection.
- Do not force Clean Architecture, Hexagonal Architecture, DDD, MVC, MVVM, microfrontends, or any named style without evidence that its forces apply.
- Do not prescribe a universal `src/` tree, extract every repeated block into a utility, or replace a sufficient conditional/callback with a class hierarchy.
- Do not migrate frameworks merely because another is preferred or newer, or turn every code smell into a system-wide rewrite. Correct the cause at the smallest adequate scope.

## Output and completion

Produce a decision-focused response scaled to the request. Omit irrelevant sections rather than filling a template with generic advice. Diagrams and folder structures may clarify an already justified design; they cannot substitute for reasoning.

**For design or approach comparison, include:**

1. **Context:** goal, scope, constraints, expected lifetime/change profile, quality priorities, material assumptions/questions.
2. **Decision:** recommendation and expensive-to-reverse choices; responsibilities and ownership; important contracts and dependency direction; relevant state, lifecycle, communication, rendering, and environment implications.
3. **Alternatives and consequences:** meaningful options, benefits gained, costs accepted, lock-in, risks, and revisit conditions. Use “X improves Y because…, but costs Z under these constraints,” not “X is cleaner/better/more scalable.”
4. **Verification and next steps:** observable contract tests, incremental refactor steps when relevant, and bounded implementation tasks only if useful.

**For review, include:**

1. **Scope and reconstructed architecture:** evidence inspected, responsibilities/connections, and limitations.
2. **Prioritized findings:** evidence location, issue, concrete change/failure scenario, blast radius, affected quality, smallest adequate correction, correction cost/trade-offs, and material uncertainty.
3. **Disposition:** what to change now, what can remain as contained debt, owner/repayment trigger where agreed, and verification. Separate local code issues from architectural findings; explicitly allow no architectural changes.

Test contracts and observable behavior, not internal representation; an internal refactor should not force unrelated test rewrites. Specify invariants and failure/cleanup behavior so another developer or agent can implement and verify bounded work independently. This is a benefit of strong boundaries, not a reason to distort architecture for AI.

For expensive-to-reverse decisions, preserve a lightweight record:

```text
Context
Decision
Alternatives
Rationale
Consequences
Revisit conditions
```

Do not require ADRs for trivial local choices. Before finishing, check that every recommendation traces to evidence or a labeled assumption, names a quality and trade-off, respects the economic horizon, and adds no unjustified mechanism. State what was verified and what remains uncertain; do not claim tests or technology checks that were not performed.
