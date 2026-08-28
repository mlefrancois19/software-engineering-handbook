# 04 — Decision-Making & Coordination

A remote team can afford slow communication, but not slow decisions. Coordination without presence means the decision process must be **written, timeboxed, and owned** — and the coordination artifacts (the board, the tickets, the RFCs) replace the hallway. This chapter is about how decisions get made and recorded; chapters 07 and 08 own the technical templates (ADR, design doc) that the process uses.

## Decision rights

In a room, you can read who decides. Remotely, decision rights must be written down or every decision becomes a group project.

| Decision | Owned by | Recorded as |
|----------|----------|-------------|
| Implementation details within a module | Author | PR (chapter 09) |
| Interface change crossing modules | Author + affected teams' sign-off | PR + design note (chapters 07, 09) |
| Architecture coherence, new technology, service split | Tech lead | ADR (chapter 07) |
| Priorities, acceptance criteria, flag removal | Product owner | Tickets, backlog (chapters 05, 06) |
| Stop-the-line, rollback, incident response | On-call | Incident timeline (chapter 12) |
| Cross-cutting contracts, security posture | Tech lead + security champion | ADR (chapter 07) |

The default state of a decision is **made by the accountable role**, not "open for infinite input." Input is a window, not an open door. When in doubt about who decides, the tech lead or product owner decides who decides — and records it.

## The async decision process

Every significant decision follows the same shape — write, review, deadline, record:

1. **Write the proposal** using the design-doc template (chapter 08) — for architecture decisions, the ADR template (chapter 07).
2. **Announce it with a comment window and a deadline.** The deadline is mandatory: `Decision by YYYY-MM-DD` in the first line of the proposal. A proposal without a deadline is a suggestion with a hangover.
3. **Reviewers comment in their own time zones.** The comment window spans the team's zones — nobody is excluded for sleeping through a live debate. Round-robin for inclusion: if two reviewers dominate, the owner explicitly asks the quiet time zones for their read.
4. **The accountable role decides at the deadline** — "disagree and commit" is binding — and **records it**: update the doc, close the thread, link the ADR/ticket (chapters 02, 07). The recorded decision is the deliverable; the discussion is the process.
5. **Re-opening a decided question requires a new proposal**, not a chat revival. New evidence, new proposal, new window, new deadline.

**No decisions in chat.** A decision made in chat is a suggestion with no record (chapter 02). If a chat thread reaches a decision, someone's job is to convert it into a ticket, PR, or ADR — that conversion is the decision.

## Decision velocity

- **The silent-decision failure**: proposals that die without a decision are the remote equivalent of a stalled hallway. The owner chases the deadline; if no one responds, the decision still happens at the deadline — silence is consent, recorded.
- **Default resolve times**: PR-sized decisions resolve within the review window (1 business day, chapter 09); design decisions within ~2 business days (chapter 08); architecture decisions within ~3 business days (chapter 07). Longer windows are reserved for genuinely reversible-cost decisions, declared up front.
- **Speed is a feature.** A good decision on Tuesday beats an ideal decision next Monday. The cost of delay compounds across time zones faster than it does in a room.

## The tracker / board as source of truth

The board is the coordination artifact (chapters 03, 05). Work that is not on the board does not exist.

- **Ticket quality**: one-sentence outcome, acceptance criteria, an owner, linked PRs. A ticket that cannot be understood by a person in another time zone is a ticket that will be re-asked in chat.
- **Lifecycle and WIP limits** follow the flow framework (chapter 05). Unenforced WIP limits reduce the board to a to-do list.
- **Updating the board IS communication.** A ticket that reflects reality replaces ten questions. Move it, comment it, link it — and your colleagues' questions vanish.
- **"Done" is defined by the Definition of Done** (chapter 05), not by a ticket status that means different things to different people.

## Coordination across time zones

- **Written handoffs between shifts** — the on-call handoff notes (chapter 12) are the model for every handoff: what happened, what's open, what to watch. A handoff that exists only in someone's head is a handoff that didn't happen.
- **Dependency visibility**: blockers are written and surfaced — one person blocked in a sleeping time zone costs the whole chain a day. "Blocked on X, needed by Y, ETA Z" in the ticket is the standard.
- **Ask one owner, not everyone.** A question goes to the accountable person — or to the documented channel for it. "Asked 5 people in 3 channels" is how you get 5 opinions and zero ownership.
- **Cross-team interface changes** (chapters 07, 09) run async review with a window; the sign-off is written, never a verbal "sure, looks good."

## Planning rhythm

- **Written priorities precede every planning meeting** (chapters 03, 05). Nobody plans against unwritten priorities.
- **Quarterly planning** = written roadmap + a sync kickoff (chapter 05). The roadmap is a hypothesis; the retro revisits it (chapters 03, 12).
- **Capacity buffers** for interrupts and incidents are planned, not discovered (chapter 05).
- Plans are public, versioned, and reviewed — the plan itself is a coordination artifact.

## Anti-patterns

- ❌ Decisions made in a live call while the people who own the decision sleep.
- ❌ RFCs open indefinitely — no deadline, no decision, work stalls politely.
- ❌ Decision reversal without updating the record — the team keeps doing the old thing while the new thing was decided months ago.
- ❌ "Asked 5 people in 3 channels" — noise instead of ownership.
- ❌ Deciding by the loudest voice — in remote, that's "loudest in the thread," and the async window exists precisely to defeat it.
- ❌ The proposal that dies silently while the work happens anyway — unrecorded decisions are the remote equivalent of water-cooler decisions, minus the water cooler.
- ❌ Updating the tracker after the fact instead of at the moment — a board that tells yesterday's story is decoration (chapter 03).