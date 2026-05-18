---
name: frontend-architecture
description: Apply pragmatic frontend architecture principles when designing, refactoring, or reviewing frontend code structure. Use this skill whenever the user mentions frontend architecture, архитектура фронтенда, проектирование фронтенда, декомпозиция, module boundaries, layers, SOLID/DRY/YAGNI/KISS in UI code, coupling/cohesion, сцепленность/связность, technical debt, legacy frontend refactoring, interfaces/contracts, dependency inversion, model/view separation, state/data ownership, or asks how to structure React/Vue/Angular/vanilla JS code so changes stay manageable.
---

# Frontend Architecture

Use this skill to make frontend code easier to change, read, test, and extend. Treat architecture as practical control over future changes, not as folder aesthetics or pattern performance.

## Core Lens

Judge design by change cost:

- Readability: can a new engineer quickly understand what code does?
- Maintainability: can the responsible place for behavior be found and fixed?
- Extensibility: can new behavior be added without rewriting tested code?
- Frontend scalability: can solutions be repeated and transferred across similar screens/features?
- Testability: can logic be isolated, dependencies controlled, and contracts tested?

Use principles as tools for concrete problems. Do not recommend SOLID, layers, patterns, or abstractions because they are "best practice". Explain what pain they reduce and what cost they add.

## Workflow

1. Establish context: project lifetime, team size, expected change vector, framework, current pain, and constraints.
2. Identify responsibilities: data/model logic, presentation logic, side effects, infrastructure adapters, and integration boundaries.
3. Inspect boundaries: public interfaces, imports, dependency direction, global/browser APIs, external services, and contracts.
4. Prefer small safe changes: isolate one responsibility, introduce one boundary, or move one dependency behind an interface before larger rewrites.
5. Verify through testability: if design is hard to unit-test or mock, dependency boundaries are probably unclear.
6. State tradeoffs: what becomes simpler, what becomes more verbose, and when not to apply the pattern.

Ask one focused question only when missing context changes the recommendation. Otherwise state the assumption and proceed.

## Architecture Smell Checklist

Use this checklist when reviewing code or deciding where to refactor first:

- One file or component owns UI, validation, API calls, formatting, storage, and notifications.
- A function grows flags like `showToast`, `strict`, `silent`, `skipValidation`, or `source`.
- Code reaches through dependencies: `a.b.c.doSomething()` instead of using `a`'s public contract.
- Behavior depends on setter/effect/callback order rather than explicit data flow.
- Business rules read browser globals, DOM, `localStorage`, current URL, current date, or network directly.
- Interfaces contain fields meaningful only in one usage context, such as list position on product data.
- `Utils`, `helpers`, or shared modules collect unrelated responsibilities.
- Abstraction exists only because code looks similar, not because responsibility or business rule is shared.
- Tests need full browser/UI setup to check pure business behavior.
- Dead or commented code stays because nobody knows whether it is needed.

Do not treat every smell as a blocker. Use smells to choose the next smallest change that reduces change cost.

## Decomposition Guidance

Separate model and view responsibilities:

- Model: source data, validation, business rules, transformations, API/domain logic.
- View: markup, styles, layout, user interaction wiring, UI state like selected tab or open dropdown.
- Computed data: derive from primary data instead of storing it, unless caching is required and invalidation is explicit.
- Dirty data: user input and external API responses. Validate at clear boundaries before treating data as clean.

Assign responsibilities at the right level. SRP does not mean "one method per class" or "one tiny file per action". A module should have a coherent reason to exist and a name that describes its responsibility without `Utils`, `helpers`, or mixed concerns.

## Boundaries And Dependencies

Use interfaces/contracts to create architectural boundaries. A boundary is useful when it lets internal implementation change without breaking consumers.

Prefer:

- One-way dependencies between layers/features where possible.
- High cohesion inside modules and low coupling between modules.
- Dependency on contracts rather than concrete browser/infrastructure APIs.
- Composition over inheritance for frontend reuse, unless there is true `is-a` relationship and real polymorphic substitution.

Watch for:

- Direct `window`, `document`, `localStorage`, routing, date/time, or network dependencies inside model/business logic.
- Reaching through another object into its internals, violating Law of Demeter.
- Temporal coupling: behavior depends on call order, lifecycle timing, or racing setters/effects.
- Interfaces containing context-specific or computed fields, such as list `index` stored on item data.

## Principle Use

Apply principles pragmatically:

- SRP: split by meaningful responsibility. Put conditions in caller when a method's job would otherwise become ambiguous.
- OCP: when adding variants, prefer extension through composition, passed functions, registries, or adapters instead of editing `if/else` chains.
- LSP: avoid inheritance for code reuse. Use inheritance only when consumers can safely use every subtype as the parent.
- ISP: keep consumer-facing interfaces narrow. Do not force implementers to provide lifecycle methods or fields they do not need.
- DIP: pass infrastructure dependencies from outside. For example, accept a `storage`, `locationProvider`, or API client instead of reading browser globals directly.
- DRY: remove duplicated knowledge/business rules, not merely similar-looking code. Similar code with different responsibilities may be better duplicated.
- YAGNI: do not add unrequested extension points. Every abstraction becomes maintenance surface.
- KISS: choose the simplest design that keeps likely changes local.

## Avoid Overengineering

Prefer no new abstraction when:

- Expected project lifetime is short and change vector is narrow.
- There is only one concrete use case and no clear second variant.
- Duplication is syntactic only, while responsibilities differ.
- A framework/store/pattern would hide logic more than clarify it.
- The team cannot name the problem the abstraction solves.

Prefer abstraction when:

- The same business rule must stay consistent across places.
- A dependency makes code hard to test or blocks SSR/non-browser execution.
- Adding a new variant would otherwise edit a tested `if/else` chain.
- A boundary lets legacy code and new code coexist during migration.

## Legacy Refactoring

For poorly structured frontend code, avoid rewrite-first advice unless the user asks or incremental change is clearly impossible.

Use this sequence:

1. Find seams: tests, public methods, routes, components, API calls, feature folders.
2. Characterize behavior with tests or snapshots before moving logic.
3. Extract by responsibility, not by file size.
4. Move browser/API/global dependencies behind small adapters.
5. Introduce TypeScript types or runtime validation around dirty data.
6. Pay technical debt as part of feature work; document debt strategy if it cannot be fixed now.

Technical debt is acceptable when there is a repayment strategy. Uncontrolled debt is the risk.

## Output Formats

For architecture advice, use:

```markdown
**Recommendation**
- Main design direction.

**Why**
- Change-management reason, not pattern name only.

**Tradeoffs**
- Costs, risks, and when this would be overengineering.

**Next Steps**
- Small ordered implementation steps.
```

For code review, use findings first:

```markdown
**Findings**
- [Severity] `path:line` Problem. Why it hurts future changes/tests. Fix direction.

**Architecture Notes**
- Boundaries, responsibilities, coupling/cohesion, and testability observations.

**Verification**
- Tests or checks reviewed/run, plus gaps.
```

For refactoring plans, use:

```markdown
**Goal**
- Change-management goal.

**Current Risks**
- Coupling, unclear responsibility, dirty data, temporal coupling, or untestable dependency.

**Incremental Plan**
- Step-by-step changes that preserve behavior.

**Tests**
- Characterization and unit tests needed before/after refactor.
```

## Examples

**Direct browser dependency**

Problem: model logic reads `window.location.pathname`.

Better: introduce `LocationProvider` and pass it in. Browser implementation reads `window`; tests pass fake provider; SSR can use server implementation.

**Flag accumulation**

Problem: `validateForm(form, showToast, strictValidation)` grows `if` branches.

Better: split validation from UI feedback. Let caller compose `validateForm(form)` and `showToast(...)`, or create a named workflow function for the use case.

**Wrong DRY**

Problem: extract one helper because arithmetic looks identical in unrelated domains.

Better: keep duplication if responsibilities differ. Extract only shared business rule or shared stable concept.

**Inheritance for reuse**

Problem: `Team extends Array` to get array methods.

Better: `Team` contains `Set` or `Array` members and exposes a narrow public contract. Team contains members; it is not an array.
