# 11 — CI/CD & Release

Automation removes the need for synchronized humans: the pipeline is the hand-off between time zones. Merging from anywhere, at any hour, is safe because the gate is a machine — reproducible builds, automated deploys, and automatic rollback on SLO breach (chapters 01, 12).

## Continuous Integration

Every PR and every merge to `main` triggers:

```
lint/format → type check → build → unit tests → integration tests
  → SAST + dependency scan → (merge queue re-runs on composed trunk)
```

Rules:
- **Green or it doesn't merge.** Required status checks are enforced by branch protection.
- Pipeline target: **< 10 minutes**; invest in caching, parallelization, test sharding when exceeded.
- Builds are reproducible: same commit → same artifact (lockfiles, pinned base images).
- Artifacts are built **once** and promoted through environments — never rebuilt per environment.

## Continuous Delivery / Deployment

Merging to `main` triggers the CD pipeline:

```
main → build & sign image → deploy staging → smoke + E2E + DAST
     → canary 5% prod (bake 30 min, auto-abort on SLO breach)
     → progressive rollout 25% → 50% → 100%
```

- Deployment is fully automated; no human gate except for designated high-risk services.
- Every deploy is a tagged, signed, immutable artifact with full provenance (commit SHA, pipeline run).
- Config is separated from code; secrets injected at runtime.

## Versioning

- **Semantic versioning** (`MAJOR.MINOR.PATCH`) for libraries and published APIs.
- Services are identified by immutable version tags (`git sha` short hash + semver), never `latest`.
- CHANGELOGs generated from Conventional Commits.

## Release strategies

| Strategy | Use when |
|----------|----------|
| Canary | Default for services with traffic; auto-rollback on error-rate/latency SLO breach |
| Blue-green | When instant switchback matters more than cost of double capacity |
| Rolling | Stateful/simple deployments where canary infra isn't warranted |
| Feature flags | Decouple feature release from deploy; gradual exposure by cohort |

## Rollbacks

- Rollback must be a **first-class, tested path**: one command / automatic on SLO breach.
- Database migrations are backward compatible so the previous app version keeps working (expand → migrate → contract, chapter 07).
- If rollback is unsafe (irreversible migration, data corruption): stop-the-line, incident process (chapter 12), fix forward with a documented plan.

## Release checklist (automated where possible)

- [ ] CI green on `main` at release commit
- [ ] Artifact signed & scanned (no critical/high CVEs)
- [ ] Staging validation passed (smoke + critical E2E)
- [ ] Feature flags default-safe; flag rollback documented
- [ ] Alerts tied to SLOs exist for new behavior
- [ ] Rollback verified (previous version runs against current schema)
- [ ] Release notes generated; stakeholders notified

## Hotfixes

Hotfixes follow the same trunk path — there is no special fast lane that bypasses tests:

1. Branch from `main`, minimal fix + regression test that reproduces the incident.
2. Fast-track review by two senior engineers (on-call + service owner).
3. Merge → pipeline deploys via canary with shortened bake time.
4. Postmortem within 48h (chapter 12).

## Environment policy

| Env | Purpose | Data |
|-----|---------|------|
| Local | Developer loop | Synthetic |
| CI | Gating | Ephemeral containers/fixtures |
| Staging | Production-like validation | Masked/synthetic production-like volume |
| Production | Real users | Real (governed by privacy policy) |

Environments are provisioned as code (IaC, chapter 13) — drift between them is treated as a bug.
