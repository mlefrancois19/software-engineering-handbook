# Software Engineering Handbook

A practical handbook describing our end-to-end Software Development Life Cycle (SDLC), built around **trunk-based development** and **continuous delivery**.

## Principles

1. **Ship small, ship often.** Short-lived branches, small batches, frequent releases.
2. **The trunk is always releasable.** `main` must build, pass all tests, and be deployable at any time.
3. **Quality is built in**, not inspected in. Tests, reviews, and automation are part of the workflow, not a phase.
4. **Automate everything repeatable**: builds, tests, quality gates, deployments, rollbacks.
5. **Decisions are documented.** Architecture decisions, design trade-offs, and runbooks live in the repository.

## Contents

| # | Chapter | Description |
|---|---------|-------------|
| 01 | [SDLC Overview](./01-sdlc-overview.md) | Lifecycle stages, roles, and definitions of done |
| 02 | [Trunk-Based Development](./02-trunk-based-development.md) | Branching model, short-lived branches, feature flags |
| 03 | [Architecture](./03-architecture.md) | Architectural principles, decision records, review process |
| 04 | [Design](./04-design.md) | API design, data design, design reviews |
| 05 | [Code Implementation](./05-code-implementation.md) | Coding standards, code review, static analysis |
| 06 | [Testing](./06-testing.md) | Test pyramid, coverage, performance, security testing |
| 07 | [CI/CD & Release](./07-cicd-and-release.md) | Pipelines, versioning, deployment strategies, rollback |
| 08 | [Operations & Continuous Improvement](./08-operations.md) | Observability & SLOs, incidents, on-call, release strategy |
| 09 | [Infrastructure](./09-infrastructure.md) | Infrastructure as code, cloud & on-prem practices |

## Quick reference: the happy path

```
Idea → Design doc / ADR → Small branch off main → TDD → PR + review
  → CI green (unit, integration, lint, security) → Merge to main
  → CD pipeline → Deploy to staging → Canary/prod behind flags
  → Observe → Iterate
```

## How to use this handbook

- New engineers: read chapters 01–02 first, then skim the rest.
- Proposing changes: open a PR against this repo; the handbook is itself trunk-based.
