# CLI Design Principles

Use this reference when a rule conflicts with project constraints or when deciding between multiple valid CLI designs.

## Scope

The Command Line Interface Guidelines cover conventional command-line programs. They are language- and framework-agnostic. They do not attempt to define full-screen terminal applications such as editors, dashboards, or terminal games.

The guide is opinionated rather than a formal standard. Its rules express strong defaults. Depart from them when the product and its users provide a concrete reason, not because implementation shortcuts make a weaker interface easier.

## Foundational Principles

### Human First

Modern CLIs are often used directly by people, not only as tiny functions inside shell scripts. Default interactive behavior should explain itself, acknowledge work, and guide recovery.

Human-first does not mean automation-last. It means explicitly supporting both audiences rather than forcing people to use a machine interface or forcing scripts to scrape a decorated human interface.

### Simple Parts That Compose

Assume every command will eventually become part of a larger system. Standard streams, exit codes, signals, plain line-oriented text, and structured formats let unfamiliar tools work together.

Composability is behavior, not aesthetics. A beautifully formatted table that contaminates piped data is less composable than plain records; a JSON mode mixed with progress text is not machine-readable in practice.

### Consistency Across Programs

Users carry expectations between CLIs. Familiar flag names, help forms, argument syntax, and signal behavior reduce learning cost and make interfaces guessable.

Consistency is a default, not a veto. If a convention creates demonstrable confusion or danger for the actual audience, choose usability deliberately and document the deviation.

### Say Just Enough

Silence can look like a hang. Raw logs can hide the one useful fact in hundreds of lines. Design output around what the user needs to know now:

- What is happening?
- Did it work?
- What changed?
- If it failed, what can I do next?

### Make Features Discoverable

A CLI should help users learn it. Good help, examples, spelling suggestions, status commands, next-step suggestions, and actionable errors turn trial and error into a productive conversation.

### Treat Interaction As A Conversation

Users commonly run a command, inspect the response, adjust the invocation, and try again. Each response should preserve context and move them toward success rather than merely reject input.

Do not silently reinterpret dangerous input. A suggestion supports learning and user control; an automatic correction can hide a logical error and establish an accidental compatibility promise.

### Be And Feel Robust

Reliability includes actual correctness and the user's confidence that the process remains under control. Validate early, respond promptly, announce waits, show meaningful progress, handle interruption, and avoid exposing raw internals for expected failures.

Simplicity improves robustness. Prefer a small number of clear states and recovery paths over layers of special cases.

### Practice Empathy

Write messages as though the program wants the user to succeed. Avoid blame, unexplained jargon, and errors meaningful only to maintainers. Give enough context to act without requiring source-code knowledge.

### Accept Chaos Deliberately

Terminal ecosystems contain contradictions. Rules sometimes conflict with old behavior, parser limitations, platform conventions, or product requirements. Make tradeoffs explicit and preserve the highest-value contract.

## Recurring Design Tensions

### Humans Versus Machines

Default to readable interactive output and provide stable machine modes. Do not make the human default permanently ugly because someone might scrape it; do not make scripts parse colorized tables because humans prefer them.

### Silence Versus Noise

Small obvious commands can be silent. Long-running or state-changing commands should normally acknowledge work and outcome. A quiet option should suppress non-essential messaging without hiding failures.

### Flags Versus Positionals

Use flags when values represent different concepts because names remove ambiguity and permit evolution. Repeated homogeneous values such as file paths work well as positionals. Two memorable positionals can be justified for a primary operation such as source and destination.

### Prompting Versus Scriptability

Prompts can make interactive use easier, but no required input may exist only as a prompt. Every interactive path needs a non-interactive equivalent through flags, arguments, files, standard input, or another explicit channel.

### Confirmation Versus Explicit Commands

Do not add confirmation reflexively to every state change. Consider reversibility, scope, remoteness, hidden consequences, and whether the command name already clearly expresses intent. Increase friction as the potential loss becomes more severe.

### Progress Versus Log Integrity

Progress UI improves perceived responsiveness but can hide useful output or corrupt CI logs. Restrict animation to terminals, preserve failure details, and favor proven rendering libraries.

### Additive Evolution Versus Bloat

Adding new flags avoids immediate breakage but can leave an incoherent interface. Prefer additive migration when practical, while periodically evaluating whether a major version or a new command model is clearer.

### Cleanup Versus Fast Interruption

Cleanup should not make Ctrl-C appear ignored. Bound cleanup and make the next run safe even if the process exited midway. A second interrupt can skip graceful cleanup after communicating the consequence.

## Four Review Questions

When uncertain, answer these in order:

1. What does the human need to understand at this moment?
2. What behavior must automation be able to rely on?
3. Which existing convention or shipped contract will users expect?
4. What happens under invalid input, failure, interruption, concurrency, and future changes?
