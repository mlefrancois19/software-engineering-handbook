# 01 — SDLC Overview

## Lifecycle at a glance

Our SDLC is continuous: there are no long-lived integration branches, no hardening phases, and no release trains. Work flows through these stages:

```
1. Discovery / Intake
2. Planning & Estimation
3. Architecture & Design
4. Implementation (trunk-based)
5. Testing & Quality Gates
6. Release & Deployment
7. Operation & Feedback
8. Retrospective / Continuous Improvement
```

## Stage details

### 1. Discovery / Intake
- Every piece of work starts as a ticket (feature, bug, tech debt, security).
- Tickets include: problem statement, user impact, acceptance criteria, and rough size.
- Anything larger than ~3 days of work **must** be decomposed into smaller increments.

### 2. Planning & Estimation
- Teams plan in short cycles (1–2 weeks) but can release daily.
- Prioritization: value vs. risk; security and reliability fixes jump the queue.
- Estimate with relative sizing (S/M/L or story points); re-estimate only if scope changes materially.

### 3. Architecture & Design
- Significant decisions require an ADR (Architecture Decision Record) — see chapter 03.
- Non-trivial features require a short design document reviewed before implementation.
- Design reviews are asynchronous by default; synchronous only when discussion stalls.

### 4. Implementation
- Branch off `main`, live < 2 days, merge behind feature flags if incomplete.
- Test-driven where practical: failing test first, then implementation.
- See chapters 02 and 05.

### 5. Testing & Quality Gates
- CI must be green before merge: unit + integration tests, linting, type checks, vulnerability scans.
- Coverage on changed code must not decrease.
- See chapter 06.

### 6. Release & Deployment
- Merging to `main` triggers the CD pipeline. Deploys are automated to staging, then production via progressive delivery (canary/blue-green).
- Releases are decoupled from features using feature flags.
- See chapter 07.

### 7. Operation & Feedback
- Everything shipped has dashboards, alerts, and logs defined *before* release.
- SLOs define expected behavior; error budgets gate feature velocity.

### 8. Retrospective / Improvement
- Blameless postmortems for every user-facing incident (within 48h).
- Team retrospectives each cycle; action items become tickets, not meeting notes.

## Roles & responsibilities

| Role | Responsibility |
|------|----------------|
| Author | Small PRs, tests, docs, responding to review feedback promptly |
| Reviewer | Timely reviews (< 1 business day), correctness, maintainability, security |
| Tech lead | Guards architecture coherence, approves ADRs, unblocks decisions |
| Product owner | Priorities, acceptance criteria, flag removal scheduling |
| On-call engineer | Monitors SLOs, first responder for incidents, files postmortems |

## Definitions of Done

**Definition of Done — a task:**
- [ ] Implemented with tests covering new/changed logic
- [ ] Lint, type check, and CI pass locally and in CI
- [ ] No new critical/high vulnerabilities introduced
- [ ] Merged to `main` (or behind a flag if partially complete)
- [ ] Docs/ADR updated if behavior or interfaces changed

**Definition of Done — a feature:**
- [ ] All tasks done
- [ ] Deployed to production (behind flag if applicable)
- [ ] Observability in place (dashboards, alerts tied to SLOs)
- [ ] Acceptance criteria validated by product owner
- [ ] Rollback path verified
