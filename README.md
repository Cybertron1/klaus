# klaus 🐕

File-based iterative project workflow as a Claude Code plugin. Named after the dog.

State lives in **files, not chat**: a design pass produces `DESIGN.md`, a plan pass produces `plan/PLAN.md`, and every iteration runs in a **fresh context** (session or executor subagent) that reads its `PROMPT.md` and exits through a `REPORT.md` — sealed only after user sign-off. Design/plan changes go to an append-only `PIVOTS.md`.

The full contract: [skills/klaus/references/WAY_OF_WORKING.md](skills/klaus/references/WAY_OF_WORKING.md).

## Install

```
claude plugin marketplace add <this-repo-url-or-path>
claude plugin install klaus@klaus-marketplace
```

Installed once per machine; available in every project. No copying WAY_OF_WORKING.md around.

## Commands

| Command | Does |
|---|---|
| `/klaus:design` | Idea/brief → `DESIGN.md` + `PIVOTS.md`. Interviews you if there's no brief. |
| `/klaus:plan` | `DESIGN.md` → `plan/PLAN.md` via parallel draft agents (risk-first / MVP-first / dependency-first). One thing per iteration, done well. Writes iter 01 PROMPT after sign-off. |
| `/klaus:execute N` | Executor agent implements per PROMPT.md, reviewer agent adversarially re-verifies acceptance criteria, you sign off, executor seals REPORT.md + next PROMPT.md. |
| `/klaus:adhoc "desc"` | Scope inserted/ad-hoc work: `NN.x` folder, light PROMPT, PLAN entry, pivot entry. Never executes. |
| `/klaus:handoff` | Park the iteration (e.g. correction pile-up at sign-off) → `CONTINUE.md`, resume fresh with `/klaus:execute N`. |
| `/klaus:status` | Current iteration, remaining plan, carry-over, binding pivots. |

Plus an auto-triggering `klaus` core skill (vocabulary, sign-off rule, automatic pivot logging) and two agents (`klaus-executor`, `klaus-reviewer`).

## The rules that carry it

1. **Scoping ≠ executing.** Talking about iteration N writes its PROMPT.md; only `/klaus:execute N` implements it.
2. **Four phases, forward only.** Execution (agents implement + auto-verify) → verification (you test, request changes freely) → sign-off (REPORT.md + next PROMPT.md sealed) → done (immutable — further changes become a new `NN.x` iteration). REPORT.md is the last artefact, never written before you say so.
3. **Append-only history.** PIVOTS.md and PLAN.md entries are never rewritten, only superseded. Inserted iterations get decimal numbers (`04.a`), never renumbering.
4. **Fresh contexts.** Executors read files, not chat. If context runs out, `CONTINUE.md` hands off to the next fresh context.
