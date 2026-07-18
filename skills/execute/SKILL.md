---
name: execute
description: Run one klaus iteration through its four phases — execution (executor + reviewer agents), verification (user-driven changes), sign-off (REPORT.md + next PROMPT.md sealed), done. Pass an iteration number.
disable-model-invocation: true
---

# /klaus:execute — run an iteration

Argument: iteration number or full folder name (`04`, `10.a`, `04-save-slots`). Kickoff commands always use the full `<NN>-<slug>` form — the slug makes the session's auto-generated title meaningful.

You are the **orchestrator**. You never implement — you spawn fresh contexts, relay honestly, and move the iteration through four phases. **Announce each phase transition to the user** (`Phase 2 — verification: …`) so they always know where they stand.

## Setup

- Resolve `plan/iterations/<NN>-<slug>/`. Missing PROMPT.md → stop; planned iterations get their PROMPT from the previous iteration's sign-off, ad-hoc ones from `/klaus:adhoc`.
- `CONTINUE.md` exists → resume: phase 1 starts from its latest "Remaining" block.
- `REPORT.md` already exists → the iteration is sealed (phase 4). Stop and point the user to `/klaus:adhoc`.

## Phase 1 — Execution (automatic)

No user involvement until it's green.

1. Spawn the **executor** (`klaus:klaus-executor`, foreground): iteration folder path, resume flag if applicable. It implements per PROMPT.md, keeps fast checks green (typecheck, unit tests, PROMPT greps), and confirms the code **runs** (built, launched, happy path exercised once) before returning its fixed-format summary. It does **not** do criterion-by-criterion acceptance verification and will not write REPORT.md.
2. Spawn the **reviewer** (`klaus:klaus-reviewer`, foreground): iteration folder path + the executor's files-touched list. It is the sole verifier — runs every acceptance criterion's check itself (including live/device/end-to-end) plus scope discipline. Verification happens exactly once, here.
3. Reviewer FAILs → SendMessage the failures to the executor to fix, re-review. Still phase 1 — the user sees green or blocked, not ping-pong.
4. Executor BLOCKED or wrote CONTINUE.md (context exhausted) → tell the user; resume later via `/klaus:execute <NN>`.

## Phase 2 — Verification (user drives)

Entered when the reviewer passes.

1. Present **both summaries verbatim** — executor summary, reviewer verdicts. No softening, no paraphrase.
2. Ask: *"Iteration NN looks done — [reviewer verdict summary]. Phase 2: review and test it. Any changes, list them — or sign off and I'll seal REPORT.md and the next PROMPT.md."*
3. User requests changes → SendMessage the list to the executor (its context holds the implementation), reviewer re-checks the delta, present again. Repeat as often as needed — changes here are in-scope, never churn.
4. This phase ends only two ways: the user **signs off** (→ phase 3), or the user **parks** via `/klaus:handoff` (corrections → CONTINUE.md, fresh session resumes at phase 1).

## Phase 3 — Sign-off (sealing)

Entered only on the user's explicit sign-off.

1. SendMessage the executor: *"Signed off. Write plan/iterations/<NN>-<slug>/REPORT.md per the REPORT template, then the next planned iteration's PROMPT.md per the PROMPT template (seed from its PLAN.md entry, enrich with the concrete paths/greps/counts you learned this iteration). Reply with the kickoff command."* **Include the reviewer's final per-criterion verdicts in this message** — the REPORT's acceptance-criteria status comes from the reviewer, not the executor's own claims. The executor writes both files from its working memory plus those verdicts.
2. Verify both files exist and REPORT.md follows the template.
3. Executor reported deviations from DESIGN.md or PLAN.md → append a `PIVOTS.md` entry (template in `${CLAUDE_PLUGIN_ROOT}/skills/klaus/references/templates/`).

## Phase 4 — Done

The iteration is sealed and immutable: REPORT.md is never amended, the executor is finished. Anything the user wants changed from here on is **new work → `/klaus:adhoc "<desc>"`** (inserted `NN.x` iteration).

**Final chat reply: the kickoff command for the next iteration, in a code block, nothing else.**

## Rules

- REPORT.md before the user's sign-off: never.
- Relay executor/reviewer output verbatim — no summarising away failures.
- Phases only move forward. Post-seal change requests never reopen the iteration.
