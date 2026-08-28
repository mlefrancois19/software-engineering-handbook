# 02 — Communication & Async Collaboration

Communication is the operating system of a remote team. This chapter defines the channels, the writing standards, and the time-zone mechanics that make async collaboration work. The principle it serves is simple: **if it can be written, it is written** (chapter 01).

## Write vs. talk — the decision rule

Writing is the default for all communication. Synchronous time is reserved for what it uniquely does well: resolving ambiguity that has already stalled in writing, live judgment calls, relationship and feedback conversations, and incident coordination (chapter 12).

| Situation | Medium |
|-----------|--------|
| Status update | Ticket, PR, or written update — never a meeting (chapters 03, 04) |
| Question with a known answer | Search the docs first; then chat |
| Design discussion | Write it up (chapter 08) — if the doc stalls, then a call |
| Architecture decision | RFC/ADR with a deadline (chapters 04, 07) |
| Code change | PR — the primary collaboration artifact (chapter 09) |
| Urgent production issue | Page / incident channel, then the incident process (chapter 12) |
| Feedback, sensitive, or career topics | Synchronous 1:1 (chapter 03) |

Escalation rule: if a topic needs **three or more back-and-forths** in chat and is nontrivial, stop chatting — write a doc or PR, or schedule a call. Chat is for exchange, not for reasoning in public.

## Channel taxonomy

Choose the channel by the *durability* of what you're saying. More durable = more readers, longer life, higher writing bar.

| Channel | Purpose | Response expectation | Escalate to | Demote from |
|---------|---------|---------------------|-------------|-------------|
| Chat (public channel) | Questions, quick coordination, announcements | ~4 business hours | Doc/PR/call after 3+ exchanges | — |
| Chat (DM) | Private/sensitive; needs a reason | ~4 business hours | Public channel + one-line summary | Public channel |
| Tickets | Work state: what, who, when | Async, per workflow (chapter 04) | — | Chat → ticket when work starts |
| PRs | Code discussion, the durable review record | 1 business day (chapter 09) | Live pairing for stalled reviews | Chat debates about code |
| Docs / repo | Proposals, specs, decisions, knowledge | Written reply in comment window | Call if the doc stalls | Chat Q&A that recurs |
| Recorded video | Demos, walkthroughs, decisions (chapter 03) | Async comments | — | Live-only demos with no recording |
| Synchronous call | Judgment, ambiguity, relationship, incidents | Scheduled | Recording + summary posted | Default for everything |

**Public by default.** Work communication lives in public channels where the team can see it. A DM needs a reason (sensitive, private, or a transient coordination detail), and anything that others could benefit from gets a one-line summary back to the channel. If something important happened in a DM, it did not happen — write it down.

## Writing craft for engineers

Writing is the core skill of this handbook. In a remote team, your writing is your presence — and your colleagues' only access to your context. The standard is **handoff quality**: your update must be enough for someone with zero context, three time zones away, to take over your work without talking to you.

- **Status updates** follow one shape: *what changed / why / what's needed from whom / by when*. A status update that cannot state what's needed is not a status update; it is a placeholder.
- **Summary first.** Title + TL;DR + links. The reader decides in five seconds whether your artifact is relevant to them; respect that and put the outcome up front.
- **Assume zero shared memory.** Link the ticket, PR, or doc every time. "As we discussed" is meaningless to anyone who wasn't in the discussion — and meaningless to the record.
- **Update the artifact, don't re-explain.** If someone in chat asks a question your doc answers, improve the doc and paste the link. Every good chat answer is a doc waiting to be written.
- **The review comment / PR description has the same bar** — context, screenshots, rollback notes (chapter 09). The PR description is read more often than the code.

## Time zones & overlap

- Publish a **team coverage map** (working hours per person, time zone) and keep it current. It answers "is it reasonable to expect an answer now?" for everyone.
- Define **core overlap hours** — the guaranteed synchronous window for the team. Everything else defaults to async.
- **Never schedule recurring events on the same person's off-hours.** Rotate times across zones, or accept the cost explicitly and say why (chapter 03).
- Follow-the-sun applies where the team spans continents — on-call handoffs and support windows shift with daylight (chapter 12).
- **Calendar transparency**: working hours posted, meetings off-limits outside them without explicit consent.
- **No decision is made while its owner sleeps.** If the accountable person (chapter 04) is in a sleeping time zone, the decision waits for their written review — or escalates deliberately with a recorded deadline.

## Chat hygiene

- **Response-time agreement**: direct questions in chat get an answer within **~4 business hours** on business days. If you will be away longer, say so; "away until Thursday" is a complete answer.
- **Thread everything.** Untheraded chat is a search wasteland and a notification storm.
- **No long debug ping-pong in chat.** Three exchanges on a bug and you move to a pairing session or a doc/PR — chat debugging buries the context exactly where nobody will find it (chapters 09, 12).
- **Notification discipline is a right.** Focus blocks, muted channels, and Do Not Disturb are legitimate and protected. Respect others' focus blocks: nothing in chat is so urgent it cannot wait four hours — genuinely urgent things are pages (chapter 12).
- **Meetings do not re-ask what's in chat or the board.** If the answer is written, the answer is the link.
- **Ask the doc, then the search, then a person.** A question answered well in chat should graduate to a doc, README, or FAQ entry — the same question answered twice in chat is a documentation bug.

## Documentation over tribal knowledge

- **Single source of truth**: one canonical place per fact. Link, don't copy — duplicate facts drift and then contradict.
- READMEs and runbooks are working documents, maintained as code (chapters 09, 12). A README that lies is worse than none: it trains people not to read.
- Knowledge that lives only in people costs the team every time that person is asleep, on vacation, or gone.

## Anti-patterns

- ❌ "Quick call to discuss" for a one-line question.
- ❌ Chat as a decision record — decisions leave chat and become tickets, PRs, or ADRs (chapter 04).
- ❌ Mention-storms and reply-all noise that bury the signal.
- ❌ Long-running group chats with no resolution or summary.
- ❌ Private DMs for work that should be public.
- ❌ Asymmetric burden — the fast writers and the loud-mouthed dominate; the async design protects the quiet and the non-native speakers. When in doubt, slow down and write it down.
- ❌ Assuming async means slow. Done in a day with full context beats done in an hour with half the context lost.