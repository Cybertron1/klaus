---
name: plan
description: Session 2 of the klaus workflow — turn DESIGN.md into plan/PLAN.md using parallel draft agents, then write the first iteration PROMPT after sign-off.
disable-model-invocation: true
---

# /klaus:plan — plan pass

Read `DESIGN.md`, produce `plan/PLAN.md`, get sign-off, then write `plan/iterations/01-<slug>/PROMPT.md`.

## The one rule that matters

**Every iteration does ONE thing, and does it well.** An iteration with "and" in its goal is two iterations. Reject grab-bag iterations ("misc fixes + polish + setup"). Small, focused, testable — a typical project is ~8–12 iterations sized S/M/L.

## Steps

1. Read `DESIGN.md` fully. If it doesn't exist, stop and point the user to `/klaus:design`.

2. **Spawn 2–3 draft agents in parallel** (general-purpose, background), each producing a full iteration breakdown from a different angle:
   - **Risk-first:** front-load the riskiest/unknown parts so pivots happen early and cheap.
   - **MVP-first:** shortest path to something end-to-end and demoable, then widen.
   - **Dependency-first:** strict build-order by technical dependency, minimal rework.

   Each agent gets: the full DESIGN.md text, the one-thing-done-well rule, the PLAN entry template (`${CLAUDE_PLUGIN_ROOT}/skills/klaus/references/templates/PLAN-entry-template.md`), and instructions to return a numbered iteration list with goal / in scope / out of scope / acceptance criteria / size / dependencies per entry.

3. **Synthesize.** Compare drafts. Take the strongest ordering as the spine; graft better-scoped iterations from the others. Every iteration must pass the one-thing test. Note (briefly, in chat) where the drafts disagreed and what you chose.

4. **Write `plan/PLAN.md`:** numbered iteration entries (template format) + the slug map at the bottom. **No template blocks in PLAN.md** — the REPORT/PROMPT/CONTINUE templates ship with the klaus plugin (`${CLAUDE_PLUGIN_ROOT}/skills/klaus/references/templates/`); duplicating them in the project just drifts.

5. **Stop. Ask the user to review and sign off on PLAN.md.** Apply requested changes, ask again.

6. **After sign-off:** write `plan/iterations/01-<first-slug>/PROMPT.md` from the PROMPT template — seeded from PLAN.md entry 01, enriched with concrete file paths and checks where the repo already offers them. Then print, as the final chat reply, only:

   ```
   /klaus:execute 01-<first-slug>
   ```

## Rules

- PLAN.md is append-only for iteration entries once signed off; scope changes later go through pivots.
- Out-of-scope bullets are mandatory — explicit non-work per iteration.
