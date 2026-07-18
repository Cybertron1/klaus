---
name: handoff
description: Park the current klaus iteration and hand off to a fresh session — collect what's done and what remains (e.g. a pile of small corrections at sign-off) into CONTINUE.md, then print the resume command.
disable-model-invocation: true
---

# /klaus:handoff — park the iteration, resume fresh

Use when the current session shouldn't finish the iteration: too many small corrections surfaced at the sign-off gate, context is bloated, or the user simply wants to stop and pick up fresh. Not a failure mode — never write a premature REPORT.md instead.

## Steps

1. Identify the active iteration folder `plan/iterations/<NN>-<slug>/`.

2. **Collect the remaining work.** Ask the user for their correction list if they haven't given it yet. Merge it with anything already known to be outstanding (reviewer FAILs, executor open items).

3. **Write `CONTINUE.md`** (template: `skills/klaus/references/templates/CONTINUE-template.md`, dated today):
   - If an executor agent did the implementation this session → SendMessage it to write CONTINUE.md itself, passing it the correction list — its context knows best what's done and where things live.
   - Otherwise write it yourself from session state.
   - **Done so far**: completed deliverables/criteria with file paths. **Remaining**: the corrections as numbered, concrete steps — each one specific enough for a fresh context with zero chat history. **Decisions taken mid-session**: anything not yet in DESIGN/PIVOTS/PROMPT.

4. `CONTINUE.md` already exists → **append** a new dated block, never overwrite earlier ones.

5. Any mid-session decision that changes design or plan → append a `PIVOTS.md` entry.

6. **Final chat reply, code block only:**

   ```
   /klaus:execute <NN>-<slug>
   ```

   (Execute detects CONTINUE.md and resumes from "Remaining" with a fresh executor. Only the session that finishes the iteration writes REPORT.md.)
