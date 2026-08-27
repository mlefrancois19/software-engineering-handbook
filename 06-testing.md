# 06 — Testing

## Test strategy

Testing is continuous and automated. The pipeline is the primary quality gate; manual testing is reserved for exploratory work only.

## The test pyramid

```
        ╱  E2E  ╲          few — critical user journeys
       ╱─────────╲
      ║ Integr.   ║        some — module boundaries, DB, APIs, contracts
     ╱─────────────╲
    ╱    Unit       ╲      many — fast, isolated, deterministic
   ╱─────────────────╲
```

| Level | Scope | Speed target | When |
|-------|-------|--------------|------|
| Unit | Single function/class, dependencies faked | < 10ms each, suite < 2 min | Every behavior change |
| Integration | Real DB/queue/HTTP within one service | < 5s per test | Every interface/contract change |
| Contract | Consumer ↔ provider API expectations | seconds | Both sides of any contract |
| E2E | Full deployed system, real user flows | < 10 min suite | Critical journeys only (top ~10) |
| Non-functional | Perf, load, chaos, security | scheduled | Per release / weekly |

## Best practices at every level

- **Deterministic**: no sleeps, no reliance on wall-clock/order/randomness without seeding; flaky tests are quarantined **and fixed or deleted within a sprint**.
- **Independent**: any test can run alone or in parallel; no shared mutable state.
- **Behavior-focused**: assert observable outcomes, not implementation details (avoid brittle mocks of internals).
- **Arrange–Act–Assert** structure; one logical assertion per test; names describe behavior (`rejects_expired_payment`).
- **Test data**: builders/factories over copy-pasted literals; realistic edge values (empty, unicode, huge, malformed).

## Coverage & gates

- Minimum: line coverage ≥ 80% overall; **changed-code coverage must not decrease** (CI gate).
- Mutation testing (PIT/Stryker/mutmut) runs nightly on domain modules to check *assertion quality*, not just execution.

## Testing in trunk-based development

- Tests for incomplete features live behind flags and are tested in both flag states.
- Feature-flag matrix tests ensure default-off safety.
- Merge queue runs the full pre-merge suite against the composed trunk state.

## Performance & load testing

- Define SLOs first (e.g., p99 < 300ms at expected peak QPS).
- k6/Gatling/JMeter scenarios run against staging on every release candidate; results compared to baseline with regression thresholds.
- Load tests reveal capacity limits — record them in the service's runbook.

## Security testing

- SAST on every PR; DAST against staging weekly.
- Dependency & container scanning (SCA) blocking critical/high.
- Secrets scanning pre-commit + CI.
- Threat model (STRIDE-lite) required for new externally exposed surfaces (see chapter 04 design doc).

## Manual & exploratory testing

- Timeboxed exploratory sessions before major user-facing releases.
- Findings become tickets immediately; nothing "tested by hand once" is considered done.
