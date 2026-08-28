# 06 — Trunk-Based Development

TBD is the integration strategy that makes a distributed, async team converge safely: `main` is the single source of truth every time zone merges into, and small, frequent integration keeps conflicts trivial for people who never sit together (chapters 01, 04). Without TBD, a remote team's work fragments into branches that only their authors understand.

Trunk-based development (TBD) means all developers integrate their work into a single branch — `main` (the *trunk*) — in small, frequent increments. There are no long-lived `develop`/`release` branches and no merge hell.

## Core rules

1. **`main` is sacred**: it must always compile, pass the full test suite, and be deployable to production.
2. **Branches live < ~2 days** (ideal: hours). If it can't be finished that fast, split it.
3. **Integrate at least daily** — rebase or merge from `main` frequently.
4. **Small PRs**: target < 200–400 changed lines; large refactors go through strangler-style increments.
5. **No direct commits to `main`.** Everything goes through a PR, even small fixes (except hotfix policy below).
6. **Incomplete features ship hidden** behind feature flags — never as dormant branches.

## Branching model

```
main ──●────●────●────●────●──→  (always releasable)
        \  /\    /\   /
         ●/  \--/  \-●          short-lived branches (< 2 days)
```

- Branch naming: `{type}/{ticket}-{slug}`, e.g. `feat/1234-checkout-retry`, `fix/2345-null-cart`.
- Linear history preferred: rebase onto `main` before merge, squash-merge by default.
- Commit messages follow [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `chore:`, `docs:`, `refactor:`, `test:`).

## Merge queue & CI

- A **merge queue** serializes merges: each PR is tested against `main` + pending PRs before entering the trunk.
- Required status checks: build, unit tests, integration tests, lint/format, type check, SAST + dependency scan.
- Target: full pipeline < 10 minutes. If slower, invest in parallelization/caching before adding tests.
- Broken trunk = **stop-the-line** event: fixing `main` outranks all other work.

## Feature flags

Feature flags decouple **deployment** from **release**.

Types:
| Type | Lifetime | Owner |
|------|----------|-------|
| Release flags | Days–weeks; removed after rollout | Product owner schedules removal |
| Ops flags (kill switches) | Long-lived | Platform team |
| Experiment flags | Duration of experiment | Data/experimentation owner |

Rules:
- Every flag has an owner, an expiry/removal ticket created **at creation time**, and a default-safe value.
- Flags are tested in **both states** (on and off) — typically via automated tests per state or contract tests on flag defaults.
- Stale flags (> 30 days for release flags) are flagged by automation and removed.
- Never let flag logic accumulate: treat flag removal as part of the feature's DoD.

## Handling long-running work

When work genuinely cannot ship incrementally:

1. **Branch by abstraction**: introduce an interface/abstraction, implement behind it, swap incrementally.
2. **Dark launch**: deploy code path enabled only internally; compare outputs (shadow traffic).
3. **Strangler pattern** for migrations: route a percentage of traffic to the new implementation, grow gradually.

## Versioning & release branches

- We version with semantic versioning tags (`v1.4.2`) applied by CI when releasing; we do **not** maintain long-lived release branches.
- Patch releases are cut from a tag on `main` (cherry-pick only if `main` has moved past a critical fix).
- See chapter 11 for the full release process.

## Anti-patterns to avoid

- ❌ "Integration branches" merged weekly → merge conflicts, big-bang risk
- ❌ PRs open > 2 days or > 500 LOC
- ❌ Disabling tests or skipping checks to get a merge through
- ❌ Flags without removal plans ("temporary" flags living forever)
- ❌ Fixing forward on production while `main` is red
