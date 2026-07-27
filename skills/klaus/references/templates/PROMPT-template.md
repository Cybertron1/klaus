# Iteration NN — <slug>

## Kind
`feature` | `spike`

- **feature** — behavior is specifiable up front → test-first applies (see Acceptance criteria).
- **spike** — discovery; the output is knowledge, not specified behavior. Test-first does NOT apply; criteria are verified manually and the findings are written down (README notes / PIVOTS entry) so the *next* feature iteration can turn them into criteria.

## Goal
<one or two sentences>

## Required reading
- `DESIGN.md` (specific sections that matter)
- `PIVOTS.md` (entries since the last iteration, plus any still-binding older ones)
- `plan/iterations/<NN-1>-<prev-slug>/REPORT.md` (if NN > 1)
- **Visual reference** (UI iterations): `mock/<screen>.html` — the signed-off mock this iteration builds toward, unless a later pivot supersedes it (cite the pivot if so).

## In scope
- bullets seeded from PLAN.md, then **elaborated with concrete file paths, grep commands, exact counts, and per-area breakdowns**. PROMPT.md is the execution brief, not a link to the plan.

## Out of scope
- bullets seeded from PLAN.md, optionally expanded with anything surfaced since (carry-over from prior REPORTs, recent pivots).

## Acceptance criteria

Every criterion carries a **verification tag**. No untagged criteria — the tag is what makes "what is tested" explicit, and it is decided here at scoping time, not improvised during execution.

- `auto` — machine-checkable, becomes a test that joins the permanent suite. Name the intended test file (the executor picks the final test name):
  - `[auto → src/services/__tests__/playback.test.ts] Position resumes within 2 s of the saved value after an app kill.`
- `manual` — genuinely not automatable. State **why** and give the **exact steps**, so it can be re-run identically by anyone:
  - `[manual — needs a real incoming call; no simulator API] Call mid-playback → audio pauses → after hangup, position is unchanged.`

Default to `auto`. `manual` is only for: hardware / OS-session events, OS-gated human gestures, physical-device install & signing, feel/perf judgement, and aesthetics. Anything scriptable on a simulator or in CI is `auto` — launching, tapping, force-quitting and relaunching, toggling network, asserting DB rows, asserting files on disk, asserting logs.

Seed the bullets from PLAN.md, then tighten with concrete checks (`grep -rn ... → empty`, `npx tsc --noEmit` clean, specific routes/screens).

## Regression suite
- The full accumulated suite from all previous iterations must stay green. It is run by the executor before handoff and independently by the reviewer.
- Command: `<the project's test command, e.g. npm test>`.

## Deliverables for this session
1. **`feature` only — write the `auto` tests FIRST**, before implementing. Run them, confirm they fail for the right reason, and report that red run.
2. Implement everything in "In scope" until the new tests are green.
3. Run the **full accumulated suite** — new tests plus every previous iteration's. All green.
4. **Stop. Ask the user before writing REPORT.md and the next PROMPT.md.**
5. After sign-off: write `plan/iterations/NN-<slug>/REPORT.md`.
6. After sign-off: write `plan/iterations/<NN+1>-<next-slug>/PROMPT.md`.
7. Print only the kickoff command for the next session as the final chat reply, in a code block:

   ```
   /klaus:execute <NN+1>-<next-slug>
   ```

## Constraints
- <project stack constraints, e.g. "TypeScript + Vite, no new global state, ...">
