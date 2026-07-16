---
name: adhoc
description: Scope an ad-hoc or inserted klaus iteration — create its folder, a light PROMPT.md, a PLAN.md entry, and a pivot entry if the design shifts. Scoping only; never executes.
disable-model-invocation: true
---

# /klaus:adhoc — scope ad-hoc / inserted work

Argument: a description of the work ("set up e2e tooling", "fix the save-slot corruption before iter 07").

**Scoping only. Do not implement anything — that is `/klaus:execute` in a fresh context.**

## Steps

1. **Slot it.** Find the current position in `plan/PLAN.md` (latest REPORT.md tells you the last finished iteration). Ad-hoc/inserted iterations get a decimal suffix next to the iteration they relate to: `04.a`, `04.b`, `10.a`. Never renumber existing entries.

2. **Enrich.** Spawn an Explore agent to gather what the PROMPT needs to be executable without this conversation: concrete file paths, grep commands and their current hit counts, relevant config. Medium breadth is usually enough.

3. **Write `plan/iterations/<NN.x>-<slug>/PROMPT.md`.** Lighter than a planned one is fine (goal, scope, out of scope, done-when, deliverables incl. the sign-off stop) — but the Explore findings go in. Use the PROMPT template (`skills/klaus/references/templates/PROMPT-template.md`) as the skeleton and cut what doesn't apply.

4. **Append a PLAN.md entry** in the right slot (PLAN-entry template) so the iteration chain stays readable end-to-end.

5. **Changes design or future iterations → append a `PIVOTS.md` entry** (template in `references/templates/`): why inserted, what it supersedes.

6. **Final chat reply, code block only:**

   ```
   Execute iteration <NN.x>. Read plan/iterations/<NN.x>-<slug>/PROMPT.md and plan/PLAN.md, then follow the deliverables in PROMPT.md exactly.
   ```

## Rules

- Something small enough to just do now (a one-line fix during verification) doesn't need this — it's in-iteration ad-hoc work, recorded in the current REPORT.
- Something that turns out plan-sized → tell the user it belongs in PLAN.md as a full iteration instead.
