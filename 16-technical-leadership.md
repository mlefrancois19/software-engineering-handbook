# 16 — Technical Leadership & Craft

Technical leadership for an Individual Contributor (IC) is not about managing people, but about managing **technical direction, quality, and growth**. In a remote-first environment, leadership is decoupled from authority and presence. It is exercised through the creation of durable artifacts and the elevation of others.

## Leading via RFCs

Architectural alignment in a distributed team cannot happen through "whiteboard sessions" or "hallway consensus." These methods exclude those not in the room and leave no record of why a decision was made. Instead, drive alignment asynchronously through **Requests for Comments (RFCs)**.

### The RFC as a Leadership Tool
An RFC is more than a technical specification; it is a mechanism for inclusive decision-making. By moving the debate to a document, you:
- **Democratize Input:** Allow engineers across all time zones to contribute their expertise without needing to be awake for a specific meeting.
- **Force Rigor:** Writing a proposal forces you to confront gaps in your logic that are often glossed over in verbal discussions.
- **Create a Decision Log:** The RFC and its subsequent comment thread become the "permanent record," preventing the team from having the same debate six months later.

### Driving the Process
To lead an RFC effectively:
- **Define the Goal:** Start with a clear "Problem Statement" and "Goals/Non-Goals."
- **Solicit Specific Feedback:** Instead of "Any thoughts?", ask "Does this approach handle the edge case of X?" or "Are there alternative libraries for Y that I missed?"
- **Synthesize and Close:** Once consensus is reached, summarize the final decision and the trade-offs accepted. Mark the document as `DECIDED` to signal the end of the deliberation phase.

## Mentorship through Review

In a remote setting, the Code Review (CR) is the most frequent and impactful point of technical mentorship. When used correctly, it transforms from a "gatekeeping" exercise into a teaching tool.

### Shifting from "What" to "Why"
A review that only points out syntax errors or style violations is low-leverage. High-leverage mentorship focuses on the underlying principles.
- **Avoid:** "Change this to a map."
- **Prefer:** "Using a map here would reduce the lookup complexity from O(n) to O(1), which is critical for this endpoint's performance. What do you think?"

### The Socratic Method in PRs
Instead of providing the answer, ask questions that lead the author to the solution. This builds the author's critical thinking skills rather than just their compliance.
- "I noticed we're calling the database inside this loop. What would happen to the latency if the list grows to 1,000 items?"
- "How would this implementation handle a network timeout during the third step of the transaction?"

### Balancing Rigor and Velocity
Mentorship should not become a bottleneck. Distinguish between **blocking issues** (bugs, security flaws, architectural regressions) and **educational suggestions** (nitpicks, alternative patterns). Clearly label the latter as `[NIT]` or `[SUGGESTION]` to signal that the PR can be merged even if the suggestion isn't adopted.

## Technical Debt Advocacy

Technical debt is an inevitable part of software evolution, but it becomes a liability when it is invisible. Technical leadership involves making the cost of debt explicit to non-technical stakeholders.

### Framing Debt in Terms of Outcomes
Product Managers and Executives rarely care about "clean code" for its own sake. To advocate for refactoring, translate technical debt into **business risk** or **velocity loss**.
- **Instead of:** "The codebase is messy and needs a rewrite."
- **Use:** "The current implementation of the billing module has become a bottleneck. Every new feature in this area now takes 3 days longer than it should because of X. Refactoring this will increase our feature velocity by ~20%."

### The "Tax" Model
Integrate debt repayment into the standard operating procedure rather than treating it as a special project.
- **The Boy Scout Rule:** Encourage the team to leave code slightly better than they found it.
- **Debt Budgeting:** Negotiate a consistent percentage of each cycle (e.g., 10-20%) dedicated to "Engineering Health." This frames maintenance as a cost of doing business, not a luxury.

## Cross-functional Alignment

Technical leaders must coordinate with Product, Design, and QA without relying on synchronous dependencies. The goal is to reach a shared understanding of the "What" and "Why" before the "How" is implemented.

### Asynchronous Coordination
Avoid the "sync-up" meeting to clarify requirements. Instead, use **shared living documents**.
- **Requirement Specs:** When a Product Manager provides a vague requirement, respond by drafting a brief "Technical Interpretation" doc. "Based on the PRD, I understand we are building X to achieve Y. My plan is to implement Z. Does this align with your vision?"
- **Design Handoffs:** Use comments directly in design tools (like Figma) to flag technical constraints early. "This animation will add 200ms to the page load; is the visual impact worth the performance hit?"

### Managing Expectations via Transparency
Prevent "surprise" delays by making the technical complexity visible.
- **The "T-Shirt Size" Forecast:** Provide rough estimates early and often. If a request moves from a "Small" to a "Large" during discovery, communicate the *why* immediately in the ticket.
- **Outcome-Based Updates:** When updating non-technical partners, focus on the *capability* delivered, not the *code* written. "The API now supports batch uploads" is more useful than "I implemented a new bulk-insert logic in the repository layer."
