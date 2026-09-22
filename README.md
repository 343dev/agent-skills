<img src="agent-skills.svg" alt="Stylized green portrait of a person wearing sunglasses, formed with horizontal glitch-like lines." title="Never send a human to do a machine's job." width="128">

# Agent Skills

This repository contains reusable [Agent Skills](https://agentskills.io/home): packaged instructions that teach AI coding agents how to handle specific workflows consistently.

## Skills

- [clig-audit](./clig-audit/) - User-invoked, evidence-based CLI codebase audit against [CLIG](https://clig.dev/). Produces a Markdown report with severity, source locations, and implementation guidance without changing code.
- [frontend-architecture](./frontend-architecture/) - User-invoked frontend architecture design and review focused on the cost of change. Guides decisions about responsibilities, boundaries, dependency direction, state, and lifecycle ownership, with explicit trade-offs and safeguards against overarchitecture.
- [perfect-code-review](./perfect-code-review/) - User-invoked, risk-driven review of proposed code changes. Verifies candidate findings against counterevidence and separates severity, merge impact, and confidence; permits zero findings.
