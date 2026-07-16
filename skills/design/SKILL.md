---
name: design
description: Session 1 of the klaus workflow — turn an idea or brief into DESIGN.md and initialise PIVOTS.md. Interviews the user if no brief exists.
disable-model-invocation: true
---

# /klaus:design — design pass

Produce `DESIGN.md` (the spec) and an empty `PIVOTS.md`. Nothing else — no PLAN.md, no code.

## Steps

1. **Find the brief.** Sources, in order: a file passed as argument, an obvious brief in the repo (`BRIEF.md`, `IDEA.md`, README section), or the idea described in the user's message.

2. **No brief found → interview mode.** Ask the user to explain the idea in their own words. Then dig — one focused round at a time, not a wall of questions:
   - What is it, for whom, and what does "done" look like?
   - Hard constraints: stack, platform, deadline, must-use / must-avoid.
   - Data model and interfaces at whatever fidelity the user can give.
   - Tone / feel, if the project has a user-facing surface.
   - **Non-goals** — push explicitly: "what are we NOT building?"
   Stop interviewing when you can write every DESIGN.md section without inventing content.

3. **Write `DESIGN.md`.** Numbered §-sections covering: scope, constraints, data model, interfaces, end states, tone, non-goals. Sections are referenced later as `DESIGN.md §X` by pivots — number them stably. Mark open questions inline as open questions rather than guessing.

4. **Initialise `PIVOTS.md`** with only a header explaining the file:

   ```markdown
   # Pivots — decision log

   Append-only, dated, newest on top. Every meaningful design or plan change lands here. Entries are never edited; a reversed pivot gets a new superseding entry.
   ```

5. **Stop.** Ask the user to review DESIGN.md before any planning happens. Do not write PLAN.md — that is `/klaus:plan`, a fresh session.

## Rules

- The design describes the *what*, not the build order.
- If the user's idea shifts during the interview, that's normal — DESIGN.md reflects the final shape; no pivot entries needed yet (the log starts at first post-review change).
