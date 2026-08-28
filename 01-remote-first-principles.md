# 01 — Remote-First Principles

Remote-first does not mean "office-optional." It means remote is the **default**: every process, ritual, and tool in this handbook is designed for people who are not in the same room, not in the same time zone, and not awake at the same hour. Co-location is the exception, scheduled deliberately for what it uniquely enables — and it must never become a hidden prerequisite for getting work done.

Most remote teams do not fail at remote. They fail at *pretending* to be remote while quietly syncing everyone to one clock. The three failure modes to watch for:

- **Syncing everyone to one clock** — meetings scheduled so that the same subset of people is always off-hours; async work quietly requires live back-and-forth to proceed.
- **Presence theater** — green dots, reaction times, and "being seen" substituting for actual delivery. Visibility is measured in artifacts, never in online status.
- **Information asymmetry** — the people in the room (or the time zone) with the loudest voice accumulate context that everyone else never gets. If a decision, a discussion, or a fact only exists in a live setting, it does not exist.

This chapter states the three operating principles that prevent those failure modes, and the two-way contract that makes a distributed team sustainable.

## The operating principles

### 1. Async by default

Communicate through **durable artifacts** — docs, tickets, PRs, recordings — so anyone in any time zone can pick up full context at any time. Writing is the default medium; a meeting is for judgment and alignment, never for status reporting.

Operating rules:

- If it can be written, it is written. Status, questions, proposals, reviews, and decisions all have a written home (chapters 02, 04).
- A reply is expected within **one business day**; a direct chat question within **four business hours** on business days (chapter 02). Absence of a reply for longer means the request is lost — say so.
- Anything said live gets a written record: meeting notes, decision capture, recording with a summary (chapter 03).
- Announcements and decisions are made where everyone can see them — public channels, tickets, or docs — never in a DM or a room (chapters 02, 04).
- A fact that exists only in someone's head (or in a chat log) is a liability. It must graduate to a doc, ticket, or README (chapters 02, 09).

### 2. Outcomes over hours

We manage **delivery, not attendance**. How, when, and where the work happens is the individual's call, within the team's coordination needs (overlap hours, on-call windows — chapters 02, 12). What is non-negotiable is the outcome and the durable evidence of it: merged PRs, closed tickets, shipped features, updated docs.

- Contribution is evaluated on **work artifacts**, not on response latency, green dots, or hours logged.
- Managers do not check in to surveil; they check in to unblock. Constant check-ins are micromanagement wearing a remote costume.
- Presence is not a proxy for progress. A ticketed, reviewed, shipped change is progress; "I've been heads-down all day" is not.
- Outcomes can also be wrong: a shipped thing that missed its goal is still visible, reviewable evidence — the retro (chapters 03, 12) is where it gets corrected.

### 3. Everything of record lives in the repo

Decisions (ADRs/RFCs), designs, runbooks, and retrospectives are **written down and versioned**. Tribal knowledge is a liability: the person who knows how the billing service works does not owe you an explanation — they owe you a README and a runbook.

- A decision that is not recorded did not happen. Recorded = updated doc, closed thread, linked ADR (chapter 04).
- Documentation is part of the Definition of Done, not a follow-up (chapter 05).
- The handbook itself lives in the repo and is maintained trunk-based — propose changes by PR, discussed on the PR.

## The engineering backbone

The three operating principles above are necessary but not sufficient. Distributed delivery only stays safe because the engineering practices in Part II enforce it mechanically:

- **Trunk-based development** (chapter 06): `main` is the single source of truth every time zone merges into; small, frequent integration keeps conflicts trivial for people who never sit together.
- **Code review** (chapter 09): the PR is the primary async collaboration artifact — where engineers teach and learn across time zones.
- **CI/CD** (chapter 11): the pipeline is the quality gate that runs identically for whoever merges, at whatever hour.
- **Observability** (chapter 12): dashboards and SLOs tell you what's happening without anyone having to ask.

People problems are solved by chapters 01–04; system problems are solved by the practices in chapters 05–13. Both halves are required.

## Trust & autonomy — the two-way contract

Remote operation runs on trust, and trust is a contract with two sides:

**The org owes every person:**
- Clear outcomes and written context — nobody should need to reverse-engineer the team's intent from chat.
- A decision process that includes them regardless of time zone (chapter 04).
- Coordination overhead kept low: meeting discipline, small PRs, automation (chapters 03, 09, 11).

**Every person owes the org:**
- Durable artifacts: tickets updated, PRs described, docs maintained, decisions recorded.
- Honesty about blockers and workload. A silent "it's fine" rots async work fastest.
- Respect for the written word as the medium of record — if you said it in a meeting, put it in the ticket.

When one side of the contract is absent, the failure mode is predictable: either micromanagement (check-ins replacing trust) or drift (artifacts rotting because nobody updates them). Name it in the retro, fix the contract, not the people.

## Remote team failure modes

- ❌ **Meeting creep as a substitute for writing** — the team holds more meetings because its docs are stale instead of fixing the docs (chapters 02, 03).
- ❌ **24/7 availability expectations** — replies expected at all hours; no posted working hours; alarms at 22:00 for a non-incident.
- ❌ **Time-zone marginalization** — recurring meetings always at the same person's 7am; decisions made while a zone sleeps (chapters 03, 04).
- ❌ **Isolation** — work-adjacent context ("so-and-so is leaving", "the client is unhappy") only exists in live calls; join calls and read the minutes.
- ❌ **Over-communication** — noise floods channels, so real signals drown; threads everywhere, decisions nowhere.
- ❌ **Decisions made in chat** — a chat decision is a suggestion with no record (chapter 04).
- ❌ **Documentation rot** — docs that contradict the code are worse than no docs; a stale README teaches people not to read (chapter 09).
- ❌ **"Quick call" as default** — every one-line question becomes a scheduled meeting; the calendar is the DM queue.

A team that names these in its retrospectives (chapter 03) and fixes the underlying practice is a remote team that is working. A team that treats them as personality problems is a remote team that is failing.