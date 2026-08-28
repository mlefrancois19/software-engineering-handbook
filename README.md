# Software Engineering Handbook

A practical handbook for operating engineering teams optimally in a **remote-first** setup. It covers how a distributed team communicates, makes decisions, and runs its rituals (Part I), the engineering practices that make distributed delivery safe and fast (Part II), and how we hire and onboard people who will thrive in it (Part III).

Remote-first does not mean "office-optional." It means remote is the **default**: every process, ritual, and tool is designed for people who are not in the same room, in the same time zone, or awake at the same hour. Synchronous time is scheduled, scarce, and earned; everything else runs asynchronously through durable artifacts.

## Principles

We optimize for three things above all: **outcomes, not hours**; **durable artifacts, not presence**; and **small, safe integration**. The principles fall into two groups:

### Operating principles (Part I)

1. **Async by default.** Communicate through durable artifacts — docs, tickets, PRs, recordings — so anyone in any time zone can pick up full context at any time. A meeting is for judgment and alignment, never for status reporting.
2. **Outcomes over hours.** We manage delivery, not attendance. Trust and autonomy are the operating system; visibility comes from work artifacts, not from being seen online.
3. **Everything of record lives in the repo.** Decisions (ADRs/RFCs), designs, runbooks, and retrospectives are written down and versioned. Tribal knowledge is a liability.

### Engineering principles (Part II)

4. **Ship small, ship often.** Short-lived branches, small batches, frequent releases. Short cycles keep integration cheap for everyone, regardless of where they work.
5. **The trunk is always releasable.** `main` must build, pass all tests, and be deployable at any time.
6. **Quality is built in, and automation carries it.** Tests, reviews, static analysis, and deployment are part of the workflow — and they run without anyone in the room.

## Contents

### Part I — The Remote Operating Model

| # | Chapter | Description |
|---|---------|-------------|
| 01 | [Remote-First Principles](./01-remote-first-principles.md) | The constitution: what remote-first means, outcomes over hours, failure modes |
| 02 | [Communication & Async Collaboration](./02-communication-async-collaboration.md) | Channel taxonomy, writing craft, time zones, chat hygiene |
| 03 | [Meetings & Team Rituals](./03-meetings-team-rituals.md) | Meeting policy, ceremonies adapted to async, meeting hygiene |
| 04 | [Decision-Making & Coordination](./04-decision-making-coordination.md) | Decision rights, async RFC/ADR flow, tracker as source of truth |

### Part II — The Engineering Backbone

| # | Chapter | Description |
|---|---------|-------------|
| 05 | [SDLC Overview](./05-sdlc-overview.md) | Lifecycle stages, roles, definitions of done, agile frameworks |
| 06 | [Trunk-Based Development](./06-trunk-based-development.md) | Branching model, short-lived branches, feature flags |
| 07 | [Architecture](./07-architecture.md) | Architectural principles, decision records, review process |
| 08 | [Design](./08-design.md) | API design, data design, async design reviews |
| 09 | [Code Implementation](./09-code-implementation.md) | Coding standards, async code review, static analysis |
| 10 | [Testing](./10-testing.md) | Test pyramid, coverage, performance, security testing |
| 11 | [CI/CD & Release](./11-cicd-and-release.md) | Pipelines, versioning, deployment strategies, rollback |
| 12 | [Operations & Continuous Improvement](./12-operations.md) | Observability & SLOs, incidents, on-call, release strategy |
| 13 | [Infrastructure](./13-infrastructure.md) | Infrastructure as code, cloud & on-prem practices |

### Part III — People

| # | Chapter | Description |
|---|---------|-------------|
| 14 | [Hiring & Onboarding](./14-hiring.md) | Hiring bar, remote interview design, onboarding, metrics |

## Quick reference: how we work

```
Idea → Written proposal (RFC/design doc, async window + deadline)
  → Small branch off main → TDD → PR (the primary async review artifact)
  → CI green (unit, integration, lint, security) → Merge to main
  → CD pipeline → Deploy to staging → Canary/prod behind flags
  → Observe → Written update → Retro → Improve
```

Communicate it in writing (01–04), build it small and green (06–11), ship it with full automation (11–13), and write down what you learned (01, 04, 12).

## How to use this handbook

- **New hires:** read chapters 01–02 first, skim 03–04, then the rest — and know that your first week is engineered to not require asking anyone anything that isn't written down (chapters 02, 14).
- **Proposing changes:** open a PR against this repo; the handbook is itself trunk-based. Discussions happen on the PR — async by default.
- **Meeting organizers:** chapters 03 and 04 are required reading before creating a recurring meeting or an RFC.