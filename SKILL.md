---
name: team-flow
description: Run a software change through three dedicated Codex agents in sequence: Architect for read-only planning, Developer for implementation/build/tests, and an independent Reviewer for final review and correction cycles. Use for feature work, bug fixes, refactors, or any code change where separation of implementation and review improves quality.
---

# Team Flow

Use the repository's custom agents to enforce separation of duties.

## Workflow

1. Start with `architect`.
   - Give it the original user request.
   - Ask it to inspect the repository and return an implementation handoff.
   - Wait for its result before implementation starts.

2. Start `developer`.
   - Pass the original request plus the Architect handoff.
   - Ask it to implement the change, run the relevant build/tests, and return its Developer handoff.
   - Wait until implementation and verification are complete.

3. Start a fresh `reviewer` agent.
   - Do not reuse the Developer thread as the Reviewer.
   - Give it the original request, Architect handoff, Developer handoff, and tell it to inspect the actual diff.
   - Wait for its verdict.

4. If the Reviewer returns `CHANGES_REQUESTED` with any Critical or Major finding:
   - Send the concrete findings to `developer` for correction.
   - Ask Developer to re-run affected verification.
   - Run a fresh Reviewer pass after the correction.
   - Limit automatic correction/re-review to two cycles unless the user explicitly requests more.

5. Finish only when:
   - verification has run,
   - no Critical/Major reviewer findings remain,
   - and the main agent can summarize the final state clearly.

## Final response contract

The main agent should report:
- Architect: one-line plan summary
- Developer: what changed
- Verification: exact commands and results
- Reviewer: APPROVE or remaining findings
- Files changed

Do not hide unresolved Reviewer findings.
