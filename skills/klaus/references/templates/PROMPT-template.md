# Iteration NN — <slug>

## Goal
<one or two sentences>

## Required reading
- `DESIGN.md` (specific sections that matter)
- `PIVOTS.md` (entries since the last iteration, plus any still-binding older ones)
- `plan/iterations/<NN-1>-<prev-slug>/REPORT.md` (if NN > 1)

## In scope
- bullets seeded from PLAN.md, then **elaborated with concrete file paths, grep commands, exact counts, and per-area breakdowns**. PROMPT.md is the execution brief, not a link to the plan.

## Out of scope
- bullets seeded from PLAN.md, optionally expanded with anything surfaced since (carry-over from prior REPORTs, recent pivots).

## Acceptance criteria
- testable bullets seeded from PLAN.md, optionally tightened with concrete checks (`grep -rn ... → empty`, `npx tsc --noEmit` clean, specific routes/screens to smoke-test).

## Deliverables for this session
1. Implement everything in "In scope".
2. Verify each acceptance criterion.
3. **Stop. Ask the user before writing REPORT.md and the next PROMPT.md.**
4. After sign-off: write `plan/iterations/NN-<slug>/REPORT.md`.
5. After sign-off: write `plan/iterations/<NN+1>-<next-slug>/PROMPT.md`.
6. Print only the short kickoff command for the next session as the final chat reply, in a code block:

   ```
   Execute iteration <NN+1>. Read plan/iterations/<NN+1>-<next-slug>/PROMPT.md and plan/PLAN.md, then follow the deliverables in PROMPT.md exactly.
   ```

## Constraints
- <project stack constraints, e.g. "TypeScript + Vite, no new global state, ...">
