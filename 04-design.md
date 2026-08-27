# 04 — Design

Design sits between "what" (requirements) and "how" (code). Good design documents make small, safe trunk-based increments possible.

## When to write a design document

Required when a change: touches more than one team/module, introduces a new external dependency or contract, involves data migration, has security/privacy implications, or is expected to take > ~1 week of work.

Not required for routine features covered by existing patterns — use the PR description instead.

## Design document template

```markdown
# Design: <Feature name>

## Problem & goals
What user/business problem? Success criteria?

## Non-goals
Explicitly out of scope.

## Proposal
High-level approach. Diagrams (sequence/architecture) where useful.

## API / interface changes
Contracts, with before/after examples and compatibility notes.

## Data model
Schema changes; migration strategy (expand → migrate → contract).

## Rollout plan
Feature flags? Phased rollout? Backward compatibility? Rollback strategy.

## Testing strategy
What levels of tests; how correctness will be demonstrated.

## Observability
Metrics, logs, traces, alerts added for this feature.

## Risks & open questions
Known unknowns; failure modes.
```

Review process: open a PR with the doc → async review within 2 business days → resolve comments → merge the doc as the record of intent. Implementation then proceeds in trunk-sized increments.

## API design best practices

- **Consumer-first**: design from the caller's perspective; write example requests/responses before code.
- **Consistent naming** and conventions across all endpoints (resource-oriented for REST; explicit, versioned messages for events).
- **Versioning**: breaking changes require a new version + deprecation window; additive changes are non-breaking by default.
- **Errors are part of the contract**: stable error codes, machine-readable details, no leaking internals.
- **Pagination, filtering, sorting** defined up front on list endpoints.
- **Idempotency**: POSTs that create resources accept idempotency keys.
- Document with OpenAPI/protobuf; generate server stubs/client SDKs from the contract; run **contract tests** in CI.

## Data design best practices

- Normalize first; denormalize deliberately with measured read patterns.
- Every table/collection has an owner, retention policy, and PII classification.
- Migrations are reversible (or forward-only with a documented recovery path) and never deployed in the same step as consuming code.
- Prefer UUIDs/public identifiers externally; keep internal surrogate keys private.

## UX/design collaboration

- Design reviews include product design for user-facing changes.
- Ship behind flags to gather feedback early (staging demos > static mockups).
- Accessibility (WCAG 2.1 AA) is an acceptance criterion, not a nice-to-have.
