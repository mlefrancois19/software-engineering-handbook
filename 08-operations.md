# 08 — Operations & Continuous Improvement

## Observability

Every service ships with, before first production release:

- **Metrics**: golden signals (latency p50/p99, traffic, errors, saturation) + business KPIs.
- **Structured logs**: JSON with correlation/request IDs; no PII in logs.
- **Traces**: distributed tracing with propagated context across service boundaries.
- **Dashboards**: one overview dashboard per service, generated from a template.
- **Alerts**: symptom-based (SLO burn rate), not cause-based; every alert links to a runbook.

A service that deployed but cannot be inspected is not released. Observability is a release gate (chapter 07), not an afterthought.

### Metrics

- **Golden signals** (Google SRE): latency, traffic, errors, saturation — the first four panels of any service dashboard. The USE variant (utilization, saturation, errors) applies to infrastructure.
- **RED method** for request-driven services: Rate, Errors, Duration. Cheaper to instrument, usually captured at the ingress/API boundary.
- **Metric types**: counters for monotonic events (requests, errors — read with `rate()`), gauges for current state (queue depth, connections), histograms for distributions (latency → server-side percentiles). Prefer histograms over summaries: summaries cannot be aggregated across instances.
- **Naming is a public API**: base units with the unit in the name (`_seconds`, `_bytes`, `_total` for counters), prefixed by service/namespace; one house convention lint-checked in CI (e.g., promlint). Renaming a metric breaks dashboards and alerts — get it right early.
- **Cardinality is the #1 killer of metrics backends**: keep label sets small and bounded. Never put request IDs, user IDs, emails, or stack traces in metric labels — that is log/trace territory. Normalize high-cardinality paths (`/api/users/{id}`). Enforce a series budget per service; a change that multiplies series count is a design decision, not a casual add.
- **Percentiles over means**: track p50/p90/p99 (p99.9 for latency-sensitive services) from histograms; a mean hides outliers. Choose bucket boundaries deliberately (exponential buckets); p99 is a distribution, not a single number.
- **Instrument at every layer**: application code (OpenTelemetry/instrumentation libraries), infrastructure (exporters, node/system agents), edge/ingress (the user-visible perspective), and business KPI counters. If the edge is not instrumented, you cannot answer "are users affected?".

### Structured logs

- JSON with correlation/request IDs; no PII in logs.
- Central aggregation (Loki, Elasticsearch, CloudWatch…) with per-source retention aligned to compliance needs; `trace_id` in every log line links logs to traces.
- Debug-level logging toggleable at runtime; nothing logged in hot paths.

### Traces

- Distributed tracing with propagated context (W3C `traceparent` / OpenTelemetry) across service boundaries.
- Sample deliberately: hot services sample (e.g., 1–10%), but always retain full traces for errors and slow requests (tail sampling).

### Dashboards

- One overview dashboard per service, generated from a shared template: four golden signals + key business KPI + SLO burn rate. Anyone should read it in under 30 seconds.
- **Dashboards as code**: provisioned from the repo (Grafana provisioning/Terraform), reviewed like code; no click-built ephemeral dashboards that die with their author.
- Few, maintained dashboards beat dozens of one-off graphs. Name and tag for discoverability.
- Dashboards inform; alerts decide. Never page people from dashboard thresholds — alerting lives in alert rules (below).

### Alerts

- Symptom-based (SLO burn rate, below), never cause-based: "disk > 80%" pages are debt.
- Every alert has a severity, an expected action, and a runbook link. An alert with no action is noise.
- **Alert, don't page**: low severity routes to chat; only what needs immediate human action pages.

## SLOs & error budgets

- Each user-facing service defines SLOs (e.g., 99.9% availability, p99 < 300ms).
- Error budget policy: budget remaining → normal feature velocity; budget exhausted → reliability work takes priority until recovered.

### SLIs — the measurable definition of "good"

- An SLO is expressed over an SLI: a ratio of good events to valid events, measured over a rolling window (typically 28–30 days).
- **Availability SLI**: successful requests / valid requests (e.g., HTTP 2xx/3xx out of all requests; decide explicitly where 4xx and timeouts land).
- **Latency SLI**: fraction of requests served within a threshold (e.g., ≥ 99% of requests complete in < 300 ms). A percentile threshold, never a mean — means hide outliers.
- Measure where the user feels it: at the API/edge boundary, end to end. Supplement server-side measurement with synthetic probers (external uptime checks) and client-side telemetry where real user experience matters.
- Only count *valid* requests: exclude traffic you deliberately ignore (health checks, pre-flight auth noise) — or include it knowingly and document why.
- An SLI nobody can explain in one sentence is too complex. The simplest ratio you can defend is usually the right one.
- Every SLO has an owning team and lives in the service catalog.

### Choosing targets

- Set targets from what users need, not industry norms. 99.9% is not a default — ask "how much downtime can the user tolerate?" and "what latency is acceptable?".
- The cost of extra nines (365-day year):

| Availability | Per year | Per month | Per day |
|--------------|----------|-----------|---------|
| 99%          | 3.65 days | 7.3 hours | 14.4 min |
| 99.9%        | 8.77 hours | 43.8 min | 1.44 min |
| 99.95%       | 4.38 hours | 21.9 min | 43.2 s |
| 99.99%       | 52.6 min | 4.38 min | 8.64 s |
| 99.999%      | 5.26 min | 26.3 s | 0.86 s |

- Each additional nine multiplies operational cost and complexity; a promised target you cannot measure is worse than no target.
- Keep internal/contract SLOs (dependencies: "DB provides 99.95%") distinct from external/user-facing ones; internal SLO failures must become visible before they become user impact.
- SLOs are product decisions: set with product management, reviewed quarterly.
- New services define SLOs before GA — part of the release checklist (chapter 07).

### Error budgets

- Budget = 100% − SLO over the window; it is the unreliability the team may spend on risk (deploys, experiments, migrations).
- Track remaining budget on the overview dashboard — everyone sees it before merging that risky change.
- Exhausted budget → stop-the-line: non-essential releases to the service are held, optional work cancelled, reliability work prioritized until the budget recovers.
- Budget exhaustion is the feature, not the failure: it is the mechanism that forces reliability investment. If budgets never move, the SLOs are too loose to be useful.

### Burn-rate alerting

The recommended alert style: watch the rate at which the error budget is consumed, not raw thresholds — "latency > X ms" pages are cause-based and noisy.

- **Page (fast burn)**: burn rate ≥ 14.4× sustained over ~1 hour — at that rate the 30-day budget is gone in ~2 days. This is a live incident.
- **Ticket (slow burn)**: burn rate ~1× over 3 days (chronic degradation) or ≥ 6× over 6 hours — act on the next business day.
- Multi-window design: a short window catches fast burns; a long window filters one-minute blips that recover on their own. A transient spike that self-heals within the short window must not page.
- Each SLO gets at least one fast + one slow alert, each linking to its runbook.

### SLO review

- Quarterly, with product: revisit targets, measurement quality, and budget policy. Tighten SLOs that never burn; relax ones that are structurally unmeetable.
- Review alert rules in the same pass — stale alerts are noise debt.

## Incident management

1. **Detect** — alerting or user report; on-call acknowledges within 5 minutes.
2. **Mitigate** — stabilize first: rollback, flag off, scale out. Mitigation > diagnosis (rollback mechanics, chapter 07).
3. **Communicate** — status page updates at defined intervals; single incident commander.
4. **Resolve** — confirm recovery via metrics.
5. **Learn** — blameless postmortem within 48 hours: timeline, root cause (5 whys), contributing factors, action items as tracked tickets with owners and due dates.

## On-call

- Rotation documented; primary + secondary; handoff notes each week.
- On-call has authority to stop the line and roll back anything.
- Alert noise is debt: any page without action is tuned or deleted within two weeks.

### Schedules & rotations

- Primary + secondary (escalation) + tertiary (manager), all recorded in the on-call tool; coverage must be 24/7 × 365 for user-facing services — schedule coverage reports catch gaps.
- Rotation length matches incident load: 7 days is typical; shorten for high-load teams (fatigue → slow responses), lengthen for quiet services.
- Swaps and overrides are made in the tool ahead of time, never arranged silently in chat — the schedule is the source of truth for who is reachable.
- Timezone-aware: global teams use follow-the-sun or at least explicit per-region ownership windows.
- Shadow rotation before solo: nobody goes on call for a system they have never operated.

### When the page fires

1. **Acknowledge within the SLO (5 min)** — acknowledge first, triage after. Acknowledgment stops escalation; it is not resolution.
2. **Assess** severity and blast radius: one service? user-impacting? cross-region?
3. **Mitigate before diagnose** — rollback, flag off, scale out. Stabilize first (chapter 07).
4. **Declare & communicate** — create the incident channel, set severity, update the status page per policy, designate the incident commander.
5. **Escalate early** — if not understood within ~20–30 minutes, pull in the secondary and the service owner. Paging early is a skill, not a failure.
6. **Resolve in the tool when actually fixed** — verified on metrics, follow-ups tracked. Resolve ≠ "I saw it".

### Handoffs

- End-of-shift handoff notes: what fired, what was done, what is still open, known issues, things to watch. Written even on quiet shifts ("no incidents; watch the X migration Wednesday").
- Handoff template lives in the repo; notes go in the on-call tool so they attach to the schedule.
- The incoming on-call reads the handoff while the outgoing is still around to answer questions — a short overlap beats a Slack ping at 02:00.

### Alert hygiene

- A page without action → tuned or deleted within two weeks (the debt rule above, enforced).
- Track MTTA and MTTR per team (the tool provides them); rising acknowledge time means noise or unclear runbooks — fix the alerts, not the people.
- Quarterly alert review alongside the SLO review: disable stale rules, merge duplicates.

### Tools

An on-call platform routes alerts to the right human, escalates on no response, and records the timeline. **One platform per org** — split on-call tools produce missed pages and duplicated schedules.

Choose on: alert-source integrations, escalation model, schedule flexibility, notification channels (push/SMS/voice), ChatOps (Slack/Teams), status pages, incident coordination depth, and pricing model (per-seat vs consumption vs self-host).

| Tool | Strengths | Best for | Notes |
|------|-----------|----------|-------|
| **PagerDuty** | Deepest alert routing & escalation; huge integration catalog; status pages | Default pick; broadest ecosystem | Industry standard |
| **Grafana OnCall** | Open source; Grafana/Prometheus-native; config as code | Grafana stack; self-host | Also in Grafana Cloud |
| **Incident.io** | Slack-native incident coordination; AI | Teams that live in Slack | Often complements another pager |
| **FireHydrant** | Runbooks, service catalog, incident management | All-in-one ops platforms | Part of Freshworks since Jan 2026 |
| **Splunk On-Call (VictorOps)** | Timeline-first console; ChatOps | Splunk shops | |
| **xMatters** | Enterprise notification; ITSM/ServiceNow | Large enterprises | Everbridge |
| **Squadcast** | SRE-focused alert routing + RCA | SRE teams | |
| **Rootly** | Slack-native + AI-generated RCA | AI-forward teams | |
| **iLert** | EU/GDPR-friendly; status pages | EU-based orgs | |

#### PagerDuty

The reference model most other tools copy — **services** (one per monitored system) collect events from **integrations** (Prometheus/Grafana/Datadog/CloudWatch/Sentry webhooks, email drop, or the Events API directly); **schedules** decide who is on call; **escalation policies** decide who gets notified and what happens on no response; an unacknowledged alert becomes an **incident**.

- **Events API v2 essentials**: every alert is a `trigger` event with a stable `dedup_key` (same key = same incident, no repeat pages), a `severity` (critical/error/warning/info), and custom `payload.details` carrying context for the responder. Send a `resolve` event when the condition clears so the incident closes without human action. Dedup on something stable (host, check name, error class), never on timestamp.
- **Schedules**: weekly rotations by default; layered schedules keep separate pools for primary/secondary/manager tiers; overrides are set for known absence (vacation) beforehand.
- **Escalation policies**: level 1 = primary (acknowledge within ~15 min), level 2 = secondary (escalated on ack timeout), level 3 = on-call manager; escalate on failure to *acknowledge*, not only on failure to *resolve*; notify by app push with SMS + voice fallback on critical paths.
- **Incident settings per service**: auto-resolve after N minutes once the alert stops firing; maintenance windows suppress noise during planned work; severity mapping (critical → page, warning → chat only).
- **Alert grouping**: bursty repeats from one source collapse into one incident — tune per service or a flaky check pages 200 times.
- **Acknowledge vs resolve**: ack takes ownership and stops escalation; resolve closes the incident and ends the timeline. Never resolve while user impact continues.
- **Handoff notes**: written at shift end in the tool (markdown supported) so the incoming on-call reads them — handoff becomes a first-class act instead of a group-chat message.
- **During incidents**: use priorities (P1/P2…), dynamic fields to capture the incident commander and affected services, stakeholder status updates, and PagerDuty status pages for external comms.
- **Analytics**: MTTA/MTTR dashboards — mine them for noise and unfair rotations.
- **Mobile**: push + SMS + voice fallback, acknowledge from lock screen; verify the do-not-disturb override works before your first page-free night.

#### Grafana OnCall

Open source (AGPL) and part of Grafana Cloud. Alerts flow Grafana Alerting/Alertmanager → integration → **escalation chain** (notify on-call schedule, wait, escalate, repeat). Schedules with recurring shifts and overrides; notifications to Slack/Telegram/Microsoft Teams/phone/SMS/webhook; mobile app acknowledges and resolves. Provisionable via Terraform and provisioning APIs — fits the repo-driven handbook. Best when observability is already the Grafana stack: it keeps the on-call vendor out of the picture.

#### Incident.io

Slack-native (MS Teams too): `/inc` declares an incident — a channel is created, roles assigned (incident commander, comms lead, scribe), the timeline auto-captures from chat, and status pages and postmortems are generated from that timeline. Workflows automate repeatable steps; AI transcribes incident calls (Scribe) and proposes next actions. On-call schedules are an add-on — many teams keep an existing pager for alert delivery and use Incident.io for coordination.

#### FireHydrant

Web platform + Slack commands: service catalog with dependency mapping, runbooks that auto-trigger steps by severity/service, integrated status pages, automated retrospectives. On-call ("Signals") is consumption-based alerting with schedules. Part of Freshworks since January 2026, folding into its ServiceOps line. Best for teams that want incident management, on-call, and a service catalog in one product.

#### Others

- **Splunk On-Call (VictorOps)**: timeline-first incident console and ChatOps; a natural fit inside a Splunk observability estate.
- **xMatters (Everbridge)**: enterprise notification platform with deep ServiceNow/ITSM integration; for large, process-heavy organizations.
- **Squadcast**: SRE-focused on-call with alert dedupe/routing and post-incident timelines and RCA templates.
- **Rootly**: Slack-native incident management with AI-generated root cause analysis and heavy workflow automation.
- **iLert**: EU/GDPR-friendly monitoring + on-call + status pages in one subscription.

## Release strategy

Chapter 07 defines the strategy menu and rollout mechanics; this section is the operational half — how to pick a strategy and, because canary is the default for any service with traffic, how to *build* systems that can be canaried safely.

### Picking a strategy

| Situation | Strategy |
|-----------|----------|
| Service with real traffic, standard change | Canary (default) |
| Instant switchback matters more than double capacity | Blue-green |
| No user traffic (batch jobs, internal tools) | Rolling |
| Exposure control decoupled from deploy | Feature flags (chapter 02) |

- Every release must be reversible: one-command rollback or automatic, and fast. A change that cannot be rolled back does not ship through the normal path — it becomes a stop-the-line fix-forward with a documented plan (chapter 07).

### Building services that can be canaried

Canary-readiness is a property of the *service*, not the pipeline. A service is canary-ready when:

- **Instances are disposable**: no valuable state on the instance — sessions, warm caches, and local queues live in shared stores (DB, KV, object storage, dedicated session store). Any instance can be killed at any moment without user-visible impact.
- **Stateless and horizontally scalable**: traffic can be split and rebalanced without affinity constraints — or affinity exists *by design* (consistent hashing on user id) so one user stays on one version across a request flow.
- **Migrations are backward compatible**: schema and data changes follow expand → migrate → contract (chapter 03) so old and new versions run side by side against the same database. Irreversible or data-reshaping migrations are explicitly not canary-able.
- **API and message contracts are version-tolerant**: both versions may call or receive from each other; request/response and event schemas change additively (schema registry on async paths) so the previous version keeps working for the whole window.
- **Readiness gating**: traffic is routed only to instances reporting ready; an instance starts serving only when its dependencies are warm.
- **Graceful shutdown**: on termination the instance deregisters from the load balancer, stops taking new work, drains in-flight requests, then exits (SIGTERM handling). A killed canary instance must not error the requests it was serving.
- **Traffic splitting exists**: the platform can route by percentage (LB weights, service mesh, gateway) and by cohort (internal users, beta segment), and move in both directions within minutes.
- **Baseline comparison exists**: the new version is measured against the old on the same axes (latency p99, error rate, saturation) for the whole bake window — the auto-abort watches the *delta* between versions, not absolute thresholds alone.

### Running the canary

- Start small and step up: 5% → bake (≥ 30 min) → 25% → 50% → 100% (chapter 07). One step at a time; each step is a separate go/no-go decision.
- Auto-abort is wired to SLO burn (burn-rate alerting, above): a breach during any step rolls the release back without waiting for a human at 03:00.
- Feature flags (chapter 02) are the second kill switch: the riskiest behavior changes ship behind a flag that toggles independently of the deploy, giving two independent rollback levers.
- The canary only catches what the baseline measures: assert error budget, error rate, and latency p99 at minimum, plus a human spot-check during the early bake.

### What not to canary

- Irreversible migrations and data-corruption risks — blue-green or stop-the-line fix-forward.
- Long-running batch jobs with an undefined tail — rolling with a monitored checkpoint.
- The release mechanism itself (pipeline, registry, IaC tooling — chapter 09): bootstrap changes ride their own canary — prove the new tooling on one environment before it carries a real release.

### Canary-readiness checklist (release gate)

- [ ] Instances disposable; all state externalized
- [ ] Schema changes backward compatible (expand → migrate → contract)
- [ ] API/event contracts additive-safe for one version window
- [ ] Readiness gating and graceful shutdown implemented and tested
- [ ] Percentage + cohort traffic split available
- [ ] New-vs-old baseline dashboards exist for the bake window
- [ ] Auto-abort on SLO burn wired and rehearsed
- [ ] Rollback tested: previous artifact runs against the current schema (chapter 07)

## Continuous improvement loop

| Cadence | Activity |
|---------|----------|
| Per PR | Review latency, pipeline time monitored |
| Weekly | Flaky test report; dependency update merge |
| Per cycle | Retrospective → action items become tickets |
| Quarterly | Architecture fitness review; ADR sweep for stale decisions |
| Per incident | Postmortem actions tracked to completion |

## Handbook maintenance

- This handbook is itself maintained trunk-based: propose changes by PR, reviewed like code.
- Quarterly review ensures it reflects reality — if practice diverges from the handbook, either fix the practice or fix the handbook within the quarter.