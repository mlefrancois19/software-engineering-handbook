# 05 — Code Implementation

## Coding standards

- **Follow the language's official style guide** (PEP 8, Google Java Style, Effective Go, Airbnb JS guide…) enforced by an auto-formatter (Black, gofmt, Prettier, Spotless…). Formatting is never discussed in review.
- **Linters + type checkers run in CI and are blocking**: ruff/mypy, eslint/typescript-eslint, golangci-lint, SpotBugs, etc. Zero warnings policy on changed code.
- **Naming**: intention-revealing; no abbreviations unless domain-standard; booleans read as predicates (`isReady`, `hasQuota`).
- **Functions**: small (one screen), single-purpose, few parameters (>3 → introduce a parameter object).
- **Comments explain *why*, not *what***. Code that needs a "what" comment should be rewritten instead. Every public API has a docstring/doc comment.
- **No dead code, no commented-out code, no TODOs without a ticket reference** (`// TODO(JIRA-123): ...`).
- **Errors**: fail fast with context; wrap errors with cause; never swallow silently; user-facing messages never leak internals/stack traces.

## Security & safety in code

- Validate all external input at the boundary; encode output per context (SQL parameterization always — no string-built queries).
- Secrets live in a secret manager / env injection — **never in code, config files, or logs**. Secret scanning runs in CI and pre-commit.
- Dependencies: pinned versions, automated updates (Renovate/Dependabot), SCA scanning blocking on critical/high CVEs.
- Least privilege everywhere: DB users, IAM roles, container capabilities.

## Test-driven development

Default loop for new logic:

1. Write a small failing test expressing the next behavior.
2. Write the minimal code to pass.
3. Refactor both test and production code.

TDD is expected for domain/business logic; pragmatic exceptions exist for exploratory spikes and thin glue code (which still get characterization tests before merge).

## Pull requests

**Author checklist:**
- [ ] Small (< ~400 LOC); if larger, split or provide a navigation comment
- [ ] Tests added/updated; full suite green locally
- [ ] Self-reviewed the diff before requesting review
- [ ] Description: what/why, screenshots for UI, flag status, rollout notes
- [ ] No secrets, no debug leftovers

**Reviewer responsibilities:**
- First response within **1 business day** (hours preferred — TBD lives or dies by review latency).
- Review scope: correctness, readability, tests adequacy, security, API compatibility, operational impact.
- Tone: comments about code, not people; label nitpicks as `nit:`; prefer questions over commands.
- Approval requires CI green. Two approvals for security-sensitive or high-risk paths.

**Merging:** squash-merge after approval; delete branch; merge queue enforces trunk health (chapter 02).

## Refactoring & technical debt

- Boy Scout rule within reason: leave touched code better, but keep PRs focused — refactors are separate PRs.
- Debt is tracked as tickets with explicit impact statements; teams reserve ~20% capacity for debt/reliability work.
- Large migrations use strangler/incremental strategies compatible with short-lived branches (chapter 02).

## Documentation in code

- README per service/module: purpose, how to run locally, how to deploy, key commands.
- Generated API docs from contracts (chapter 04) — hand-written endpoint docs are forbidden duplication.
- Runbooks linked from alerts (chapter 08).
