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

## Discuss publicly — it's how a remote team stays smart

Discussion is where a bad decision gets caught before it costs anything. In a remote team, discussion happens where the team can see it — the channel, the thread, the PR — or it does not happen at all. "Public by default" is not bureaucratic caution; it is how a distributed team stays smarter than any of its members.

Public discussion is what delivers:

- **Better decisions.** An open thread attacks a problem with the team's full context: blind spots are caught before they ship, divergent views are heard before convergence, and the quiet specialist in another time zone gets a written slot to weigh in (chapter 04).
- **Collective ownership.** Context that lives in one DM is a bus factor in chat form. Discussion in the open builds the shared map a co-located team absorbs by osmosis.
- **Free teaching.** Every public discussion is an unplanned mentorship session (chapters 14, 16), and its record lets someone three time zones away get the same lesson later.
- **A record that costs nothing.** Public discussion is async-compatible by construction (chapter 01): it happened, it is dated, it is searchable. Private discussion leaves no record and must be re-explained to every stakeholder who missed it.

The cost of private discussion is measurable: two engineers in parallel DMs converging on different answers; a "decision" that survives only in a chat log and gets re-litigated at the first question (chapter 04); information asymmetry (chapter 01) growing between the people in the conversation and the team.

Where "public" means:

- Public = visible to the team in the team's channels, or threaded on the artifact (PR, ticket, doc). Not the whole company, not the world.
- The exceptions are narrow and named: compensation, performance, personal and career topics go to the 1:1 (chapter 03). Everything else defaults to public.
- "I'll DM you so I don't bother the channel" is inverted: the channel is where the answer belongs, and a good answer graduates into the artifact (the writing-craft rules below).

## Platforms — public channels over private conversations

Slack (or your async platform) is where most of the team's discussion visibly lives, so the channel-vs-DM choice carries the most daily weight. The rule: **the channel is the discussion's home; the DM is a doorway, not a destination.**

- **Start in the channel.** A question or a proposal opens in a public thread. Only what genuinely belongs one-to-one moves to a DM — and comes back with a one-line summary to the thread.
- **Answer where you were asked.** Replying "let me DM you" to a public question deletes the answer from everyone else's context. If the thread is the wrong home, move the topic (to a doc, PR, or call — the escalation rule above), never hunt it into a DM.
- **A DM needs a reason** — sensitive or private content, transient coordination (times, links), relationship and career topics. If a DM exchange produces a fact, a decision, or a reusable answer, it is summarized to the channel or threaded into the artifact (chapter 04).

Routing by information type:

| Information | Slack home | Never |
|-------------|------------|-------|
| Announcement or decision (chapter 04) | Purpose-built public channel, with a link to the artifact | DM; a ping that replaces the record |
| Question with a known answer | Search, then a public Q&A thread | DM — the answer should graduate to the docs |
| Technical / design discussion | Public thread linked to the doc or PR — or the PR thread itself (chapters 08, 09) | DM; past three exchanges it becomes a doc or a call |
| Coordination (who/what/when) | Project channel | DM for anything the team could reuse |
| Status | Ticket / board (chapter 04) | Channel messages that duplicate the board |
| Sensitive, personal, career | 1:1 or a reasoned DM (chapter 03) | Public channel |

Platform hygiene (the Slack edition of the chat hygiene rules later in this chapter):

- **Broadcasts are spent currency.** `@channel`, `@here`, and `@everyone` are only for things everyone must act on — a decision, an incident (chapter 12). An FYI ping trains the team to mute you.
- **Bots get their own channels.** CI output, deploys, alerts (chapter 12), and dashboards post to purpose-built channels, never to the team's main channel, which is for humans.
- **Name channels by purpose; one purpose each.** A new question is a thread, not a new channel; a channel that answers two different "who do I ask?" questions is two channels pretending to be one.
- **Status is a contract, not theater.** Update your status when away and keep working hours honest (chapter 01). And the reverse: presence is not availability — "I saw you online" is a discipline failure, not a question.
- **Conclude your threads.** A thread that reaches agreement ends with a one-line outcome and a link to the artifact; a thread with no conclusion is a sock drawer.

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

## The remote discussion setup — environment & tools

Synchronous time is scarce and earned (chapter 03). When a discussion earns it, the environment must not burn it: a discussion that opens with ten minutes of "can you hear me?" spent its budget before it started. Four tiers, cheapest wins first:

**Hardware**

- The microphone is the single most important remote-communication purchase. A bad mic makes every participant pay for it; a bad camera is a lesser sin.
- Headphones or a headset kill echo and feedback for the whole room. On a laptop's speaker-and-mic in an open room, you are a metronome, not a participant.
- A machine that survives video, a screen share, and a compile without sounding like a jet engine. The fan is warning you; quiet it.

**Space**

- Quiet, lit from the front (a window behind you is a silhouette factory), and low on visual noise. A stable, boring background is a courtesy to everyone staring at it.
- Discussions go in interruption-proof windows — the same discipline that protects deep work protects them (chapter 03).

**Network**

- Wired, or Wi-Fi with honest signal. Know your VPN's latency penalty and route around it for calls.
- Have a fallback ready: phone audio, or video off / audio on. "My audio dropped" is a real-time failure with a real-time cost — recover fast, don't narrate (chapter 12's incident tempo applies).

**Platform**

- One agreed meeting platform the whole team knows — join, share, and record are muscle memory, not a weekly investigation.
- Screen share the right window, prepared before the call. Hunting for the right tab is dead air.
- A shared editor or pairing tool for "let's look at the code" (chapter 09) — the discussion moves there, not into talking about screenshots.
- A virtual whiteboard for genuinely visual reasoning — and the result is captured into the doc, because a whiteboard is not a durable record (chapter 03).
- Recording with transcript for discussions that matter, posted with a summary (chapter 03) — so the live discussion satisfies "everything of record" (chapter 01).

**Hygiene**

- Test audio and video before a scheduled discussion; arrive a minute early if the platform is new to you.
- Mute by default, unmute to speak. On a shared line, the mute button is capacity.
- Camera norms are explicit and per-person: on for discussion, off when focusing — agreed in advance, never a presence-theater scorecard (chapter 01).
- Names and pronouns on display; never a participant identified as "the one with the dog."
- Links live in the agenda before the call (chapter 03) — found before the call, not pasted into chat during it.

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
- ❌ A public question answered privately — "let me DM you" deletes the answer from the team's context (platforms, above).
- ❌ `@channel` / `@here` / `@everyone` for FYIs — broadcast pings are spent currency.
- ❌ Bots and alerts in the main channel — noise that drowns the human signal (chapter 12).
- ❌ Discussions that start with "can you hear me?" — the environment burned the sync budget (chapter 03).
- ❌ Laptop speaker-mic in an open room for a team discussion — headset or dial-in, always.
- ❌ Calls where links, agenda, and the screen share are discovered during the call — dead air is a sync-time tax (chapter 03).