---
name: klaus
description: Core rules for the klaus file-based iterative workflow. Use whenever a project contains DESIGN.md, PIVOTS.md, or a plan/ directory, or when the user mentions iterations, PROMPT.md, REPORT.md, CONTINUE.md, pivots, sign-off, or scoping/executing an iteration. Governs vocabulary (scope ≠ execute), the sign-off rule, pivot logging, and session handoffs.
---

# klaus — core workflow rules

The full contract lives in `references/WAY_OF_WORKING.md`. Read it when you need detail beyond this summary. Templates live in `references/templates/` — always copy the exact template, never paraphrase from memory.

## The shape

State lives in **files**, not chat. Each session reads only the inputs it needs and writes a small, fixed set of outputs:

- `DESIGN.md` — the spec (the "what"). Body mutable only via pivots.
- `PIVOTS.md` — append-only, dated decision log. Newest on top. Never edit old entries; supersede them.
- `plan/PLAN.md` — master iteration list. Append-only for iteration entries.
- `plan/iterations/NN-<slug>/PROMPT.md` — entry contract for one iteration.
- `plan/iterations/NN-<slug>/REPORT.md` — exit contract, written only after sign-off.
- `plan/iterations/NN-<slug>/CONTINUE.md` — mid-iteration handoff when work spans sessions.

## Vocabulary (binding)

- **Iteration** = bounded unit of work, own PROMPT.md (entry) and REPORT.md (exit), executed in a **fresh context** (new session or executor subagent) that reads files, not chat history.
- "plan / scope / set up iteration N" said outside N's execution → **write its PROMPT.md only**. Do NOT implement inline. Scoping ≠ executing.
- "execute / run / continue iteration N" → implement per its PROMPT.md.
- Ambiguous → default to scoping a PROMPT, not carrying context across. Sizeable new work mid-session → prefer an inserted/ad-hoc iteration PROMPT over implementing inline.

## The sign-off rule (most important)

When an iteration's implementation is done and verified: **STOP. Do not write REPORT.md or the next PROMPT.md yet.** Ask:

> "Iteration NN looks done — acceptance criteria green, [short verification note]. Want to review before I write REPORT.md and the next PROMPT.md? Any tweaks to land first?"

Only after explicit sign-off: (1) write REPORT.md, (2) write the next iteration's PROMPT.md, (3) print the kickoff command (`/klaus:execute <NN+1>-<next-slug>` — always with slug, it names the next session) as the final chat reply, nothing else. Changes requested during sign-off are in-scope: apply, then ask again. REPORT is the *last* artefact, never the first.

Every iteration moves through four user-visible phases, forward only:

1. **Execution** — executor implements and hands off only code that *runs* (fast checks green, happy path exercised once); adversarial reviewer does the acceptance verification, criterion by criterion. Verification happens once, by the reviewer. No user involvement.
2. **Verification** — user reviews and tests; change requests are applied in-scope, repeat freely.
3. **Sign-off** — user approves; REPORT.md + next PROMPT.md are written (sealing).
4. **Done** — the iteration is immutable. REPORT.md is never amended; any further change is new work → ad-hoc/inserted iteration (`NN.x`).

## Pivot logging (automatic, no command)

Log a `PIVOTS.md` entry (template: `references/templates/PIVOT-entry-template.md`) whenever any flow detects a meaningful design or plan change:

- an iteration deviates from DESIGN.md or its PLAN.md entry,
- an ad-hoc/inserted iteration changes design or future iterations,
- the user redirects the design mid-session,
- a side session produces a decision that affects the project.

Never rewrite DESIGN.md sections the project has moved past — log the pivot and mark the section superseded.

## Inserted iterations

New work that must land before the next planned iteration gets a decimal slot (`04.a`, `04.b`) — never renumber the plan. Own folder + PROMPT + REPORT + PIVOTS entry + PLAN.md entry in the right slot.

## Side sessions

Quick fixes/questions: no PROMPT/REPORT needed. But if they produce a project-affecting decision → PIVOTS entry. If they uncover work → PLAN.md entry. If they balloon → promote to ad-hoc iteration (`/klaus:adhoc`).

## Session end mid-iteration

If the session is ending and the iteration's PROMPT.md is not fully delivered: write/append `CONTINUE.md` (template in `references/templates/`) instead of a premature REPORT or compressed quality. Append new dated blocks; never overwrite earlier ones. Only the final session of an iteration writes REPORT.md.

## Commands

- `/klaus:design` — design pass → DESIGN.md + PIVOTS.md
- `/klaus:plan` — plan pass → plan/PLAN.md, then iter 01 PROMPT after sign-off
- `/klaus:execute N` — run an iteration via executor + reviewer agents
- `/klaus:adhoc "<desc>"` — scope an ad-hoc/inserted iteration (PROMPT only, no execution)
- `/klaus:handoff` — park the current iteration in CONTINUE.md, resume in a fresh session
- `/klaus:status` — where the project stands
