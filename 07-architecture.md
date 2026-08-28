# 07 — Architecture

ADRs are the async decision records that make distributed architecture work: every expensive-to-reverse decision is written down, reviewed in a comment window that spans time zones, and decided by a deadline (chapter 04). The architecture review path below is intentionally async-friendly — no decision requires a room or a live debate.

## Architectural principles

1. **Start simple.** A modular monolith is the default. Extract services only when there is a proven need (independent scaling, team boundaries, divergent tech).
2. **Boundaries over layers.** Organize code around business capabilities (modules/bounded contexts), not technical layers alone.
3. **Dependencies point inward.** Domain/business logic must not depend on frameworks, databases, or transport (hexagonal / ports-and-adapters).
4. **Design for failure.** Every external call needs a timeout, retry budget, and circuit breaker; every service degrades gracefully.
5. **Automate architecture fitness.** Enforce rules with tools (dependency-cruiser, ArchUnit, import-linters, module boundary checks) in CI — not by convention alone.
6. **Evolutionary over big-design-up-front.** Architecture is a series of small decisions validated by feedback.

## Decision records (ADRs)

Any decision that is expensive to reverse requires an **Architecture Decision Record**:

- Stored in `docs/adr/NNNN-title.md`, numbered sequentially, never renumbered.
- Status lifecycle: `Proposed → Accepted → Superseded by NNNN`.

**ADR template:**

```markdown
# NNNN. Title

- Status: Proposed | Accepted | Superseded by NNNN
- Date: YYYY-MM-DD
- Deciders: @a, @b

## Context
What forces are at play? What problem are we solving?

## Options considered
Option A — pros/cons
Option B — pros/cons

## Decision
What we decided and why (reference options).

## Consequences
Positive, negative, and what this constrains going forward.
```

**When an ADR is required:** new datastore/technology choice, new service extraction, cross-cutting protocol/API contract changes, anything touching security posture, anything expected to cost > ~1 person-week to reverse.

## System design guidelines

### Modularity
- Modules communicate through explicit public interfaces; internal types/functions stay private.
- Shared libraries require an owner and semantic versioning; prefer duplication over premature shared abstractions across team boundaries.
- Circular dependencies between modules are build errors.

### Data
- One logical owner per data set; other services read via API or events, not by joining directly into another service's database.
- Schema migrations are backward compatible and deployed *before* the code that uses them (expand → migrate → contract).
- Prefer append-only/event-sourced designs only where the audit/replay requirement justifies the complexity.

### Interfaces & integration
- Contracts are versioned and explicitly documented (OpenAPI, protobuf, AsyncAPI).
- Backward compatibility is mandatory within a major version; breaking changes follow a deprecation window.
- Use idempotency keys for all non-idempotent operations exposed over networks.

### Non-functional requirements
- Define SLOs (availability, latency) for user-facing paths before building them.
- Capacity planning: know expected QPS/data volume and validate with load tests (chapter 10).

## Architecture review

| Change | Review path |
|--------|-------------|
| Within a module, reversible | Normal PR review |
| Cross-module interface change | PR review + affected teams sign off |
| New technology / service split / data model migration | ADR + async architecture review |
| Security-relevant | + security champion review |

Reviews are timeboxed: if no consensus in ~3 business days, the tech lead decides and records it in the ADR ("disagree and commit" — chapter 04).

## Fitness functions (enforced in CI)

- Dependency direction rules (e.g., `domain` may not import `infrastructure`)
- Public API surface diff check (breaking-change detection)
- Cyclomatic complexity thresholds on changed files
- Package size/coupling metrics trending (advisory)
