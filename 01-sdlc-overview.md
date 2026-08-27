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

## Continuous integration

Continuous integration (CI) is a **practice, not a tool**: every change merged to `main` triggers an automated build and full verification, and the trunk is, at all times, in a known-good, releasable state. CI servers (GitHub Actions, Jenkins, Buildkite…) are just enablers — the practice is the discipline of integrating small, integrating often, and never allowing the trunk to stay broken.

### Why CI beats the alternatives

- **The cost of a defect grows with its age.** A bug caught minutes after it lands is fixed in minutes, with the author's context still hot, and ships in the same batch. A bug caught at release time costs a rollback and an incident (chapter 08). A bug caught weeks later, after others have merged on top of it, costs archaeology. CI keeps defect age at hours, not weeks.
- **Integration is the riskiest moment — CI makes it happen daily instead of eventually.** Long-lived branches and big-bang merges do not avoid integration problems; they postpone and compound them. Merging two weeks of parallel work at once produces conflicts and breakage that are expensive, demoralizing, and usually discovered by the wrong person. Merging a day's worth of small diffs keeps the trunk green and the conflicts trivial.
- **Feedback is immediate.** Verification runs in minutes (chapter 07), so a problem surfaces while the change is still the author's active work. Nightly builds hand you yesterday's problems, by which time you have built more code on top of them.
- **Small diffs and healthy reviews.** Continuous integration rewards small PRs (chapter 05): reviewers check small, green changes instead of thousand-line firehoses, and review latency stays low because nothing queues up behind a broken trunk.
- **It is the base layer for everything else in this handbook.** Trunk-based development (chapter 02), CD, canary releases, and instant rollbacks (chapter 07) all assume the trunk is always releasable — which is exactly what CI enforces. Without CI, CD is just "deploying broken builds faster".
- **"Done" means "merged and green".** Work sitting in an un-integrated branch is inventory, not progress — and everyone is still paying for it. CI makes the state of the product visible and honest at all times.

### How it compares to the alternatives

| Approach | Integration moment | Integration risk | Problem feedback | Verdict |
|----------|-------------------|------------------|------------------|---------|
| Waterfall / phased | End of each phase | High | Weeks–months | Rejected |
| Long-lived branches + big-bang merge | Once, at the end | High — conflicts + compound breakage | Days–weeks, discovered by whoever merges last | Rejected |
| Nightly build only | Daily, unattended | Medium — next-day problems, code stacked on top | Next morning | Insufficient |
| Release trains / milestone baselines | At scheduled cutoffs | Medium — batch merge pressure | Days | Insufficient |
| **Continuous integration (this handbook)** | **Every merge to `main`** | **Low — tiny diffs, green trunk** | **Minutes** | **Default** |

### What keeps CI honest

- **Speed is a feature, not a nicety.** A build that takes much longer than ~10 minutes gets skipped, gamed, or waited-on impatiently (chapter 07). Caching, test sharding, and parallelism are CI investments, not luxuries.
- **Flaky tests are poison.** A red suite that nobody believes becomes noise, and noise trains people to ignore the trunk. Fix or quarantine flaky tests immediately (chapter 08, weekly flaky report).
- **Incomplete work must not block the trunk.** Work that is not ready to ship merges behind feature flags (chapter 02) so `main` stays green and releasable at all times.

## Agile methodology

Agile is a **mindset, not a procedure**: deliver working software in small increments, inspect the result, and adapt — to reality, to feedback, and to the team itself. The frameworks below are implementations of that mindset. This handbook's lifecycle is deliberately agile (short cycles, feedback loops, retrospective as a first-class stage — step 8 above); which framework a team runs matters less than running one honestly.

### Principles

- The four manifesto values: individuals and interactions over processes and tools; working software over comprehensive documentation; customer collaboration over contract negotiation; responding to change over following a plan.
- Working software is the measure of progress; sustainable pace; self-organizing teams; regular reflection and adjustment (inspect & adapt).
- Agile and continuous delivery are the same idea at different levels: small batches and fast feedback appear at the product level (chapter 02) and at the integration level (continuous integration, above).

### Scrum

- **Shape**: fixed-length iterations (sprints, typically 1–4 weeks); three roles — product owner, scrum master, developers; ceremonies — sprint planning, daily standup, review, retrospective, backlog refinement; artifacts — product backlog, sprint backlog, increment.
- **Fits**: product feature work with a stable team and a product owner who can make priority decisions continuously; organizations that need a time-boxed rhythm to force decisions.
- **Consider if adopting**: ceremony overhead is real — a small team can spend a third of a day in planning per sprint; sprint commitment can ossify into deadline pressure (velocity is an estimation tool, never a productivity goal); the sprint boundary is a *planning* boundary, not a release gate — with CD (chapter 07) we release when ready, not when the sprint ends; product-owner availability is the most common failure mode (priorities stagnate mid-sprint); support and incident interruption needs an explicit capacity buffer or sprints become fiction.

### Kanban

- **Shape**: continuous flow instead of iterations — the board visualizes workflow states; work is *pulled*, not pushed; WIP limits cap how much is in flight; explicit process policies and classes of service (standard, expedite, fixed-date); flow metrics — cycle time, throughput, lead time.
- **Fits**: ongoing operations, support, and maintenance where work arrives continuously and unpredictably; mature teams that no longer need a time-box to stay honest; anything where sprint "commitments" would be fiction.
- **Consider if adopting**: WIP limits are the mechanism — unenforced limits reduce kanban to a fancy to-do board; with no time-box there is no built-in reflection, so retrospectives must be deliberately scheduled or they quietly die; policies must be explicit (what happens when an expedite item appears?); a metrics culture is required — cycle time that nobody watches is decoration; on-call and interrupt work is absorbed via expedite lanes and capacity monitoring.

### Scrumban

- **Shape**: scrum cadence (planning, review, retrospective) on top of kanban flow — WIP limits, pull-based work, no sprint commitments; often adds lane-based classes of service and on-demand planning.
- **Fits**: teams doing a mix of product and operational work; scrum teams that feel sprint commitments no longer add value; the common end-state of teams that start with scrum and mature their flow.
- **Consider if adopting**: ceremony creep is the risk — keep only the parts that change behavior (planning + retrospective + WIP limits); the cadence/flow tension must be managed consciously (planning becomes "start new work", not "promise delivery"); roles stay loose but someone must own the WIP limits and policies — a flow manager, or the scrum master wearing that hat.

### XP & Lean

- **XP (extreme programming)** contributes the engineering practices this handbook already assumes: TDD, pairing/collective ownership, continuous integration (chapters 05, 06). Adopt the practices even when the board and the cadence come from another framework.
- **Lean software development** is the intellectual parent of kanban: eliminate waste, amplify learning, decide as late as possible, deliver fast, build quality in, optimize the whole. It is a useful lens for retrospectives (chapter 08) — every recurring waste in the flow is a candidate for elimination.

### Choosing a framework

| Dimension | Scrum | Kanban | Scrumban |
|-----------|-------|--------|----------|
| Iterations | Fixed sprints (1–4 weeks) | None — continuous flow | Optional, for rhythm only |
| Work limiting | Sprint commitment | WIP limits (pull) | WIP limits + planning cadence |
| Roles | PO, scrum master, developers | Flow manager (optional) | Loose scrum roles + flow owner |
| Ceremonies | Planning, standup, review, retro, refinement | Board + policies; retros must be scheduled | Planning + retro (on demand) |
| Best fit | Product feature work | Ops/support/continuous work | Mixed product + ops |
| Primary metric | Velocity (estimation only) | Cycle time, throughput, lead time | Both, kept visible |
| Main risk | Ceremony overhead; sprint pressure | WIP limits ignored; retros die | Ceremony creep; cadence/flow tension |

- **Match the framework to the work, not to an identity.** A well-run kanban beats a sloppy scrum; a team that copies scrum ceremonies without changing behavior has adopted theater, not agile.
- **Team size and maturity matter**: small teams (2–4 people) rarely need scrum's ceremony; large teams need explicit coordination (scrum-of-scrums or per-service boards); new teams often benefit from scrum's structure and then relax into scrumban.
- **Interrupt rate decides a lot**: teams with heavy on-call/support load should prefer flow-based frameworks and reserve capacity explicitly, or sprints become fiction.
- **Metrics are for steering, not for grading**: velocity and cycle time are team-internal instruments (chapter 08's continuous improvement loop) — using them as targets invites gaming.
- **Remote or async teams**: the board is the source of truth; ceremonies must be async-friendly; the daily standup becomes a written update or a short async check-in.
- **Evolve deliberately**: the retrospective is the mechanism for changing the framework itself — tweaking sprint length, WIP limits, or classes of service each cycle is the process working, not a sign it failed.

### Measuring velocity

Velocity is how much *completed* work a team ships per iteration — the team's own forecast of sustainable capacity. It is a planning instrument, not a performance report card.

**How to measure**

- Count only work that meets the Definition of Done (below): a story in review is not done, and scope that is not finished and accepted earns no points.
- Estimate on one consistent scale: relative sizing (S/M/L mapped to numbers, or story points) with a single team-wide scale. Estimates belong to the team — when the team composition changes, re-calibrate and re-baseline.
- Report a rolling average of the last 3–5 sprints: velocity is a trend, not a single sprint — one sprint is noise, not signal.
- Exclude anomalies deliberately: a first sprint with a new team or a sprint eaten by incidents should be flagged, not silently baked into the average.
- Split properly: a story that "was nearly done" and carries half points into the next sprint is a splitting failure, not velocity. Points are earned only on completion.
- Use the measure that fits the framework: points for scrum (points per sprint), throughput and cycle time for kanban/scrumban. Points make no sense without an iteration — flow-based teams measure flow instead.

**What velocity is for**

- Forecasting: "the team completes ~N points per sprint → this backlog is roughly Y sprints away". Rough, honest, and useful — never a promise.
- Capacity planning for the next iteration, including an explicit buffer for interrupts and incidents.
- Trend monitoring across quarters in the retrospective (chapter 08): is the team sustainably faster or slower over time?

**What it must never be used for**

- A target ("velocity ≥ 40") — targets turn estimates into negotiation and reward inflation; work expands to fill the estimate.
- Comparisons between teams — scales differ per team, so a "fast" team may just estimate larger. Cross-team velocity comparisons are meaningless and corrosive.
- A KPI, incentive, or performance metric — velocity is team-internal; grading it invites gaming.
- A claim about value or quality — finishing more points is not delivering more value; read velocity alongside defect and incident rates (chapters 06, 08).

**The flow-based alternative**

Kanban and scrumban teams drop points entirely: throughput (completed items per week) plus cycle time and lead time are the steering metrics (Choosing a framework, above), and WIP limits are the control. The same rules hold — signals are trends, never targets.

**Delivery-system velocity (DORA)**

For the health of the delivery system as a whole, complement sprint velocity with the DORA metrics: deployment frequency, lead time for change, change failure rate, and time to restore (chapters 07, 08). Sprint velocity answers "can the team plan the next iteration?"; DORA answers "is the delivery system healthy?".

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
