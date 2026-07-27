# Project Workflow — Guardrails

How we work together on a project from zero to shipped. Drop this file at the root of any new repo; it is the contract between user and assistant for the whole build.

---

## 0. The shape

```
idea / brief
   │
   ▼
[Session 1]  Design pass         →  mock/ (clickable HTML mock, UI projects)
                                  →  DESIGN.md         (the "what", drafted from the mock)
   │
   ▼
[Session 2]  Plan pass           →  plan/PLAN.md      (the "in what order")
   │
   ▼
[Sessions 3…N]  Iteration sessions
                  ├─ read PROMPT.md
                  ├─ implement
                  ├─ verify
                  ├─ → STOP, ask user before writing REPORT.md / next PROMPT.md
                  └─ write artefacts only after user sign-off
```

State lives in **files**, not in the chat window. Each session reads only the inputs it needs and writes a small, fixed set of outputs.

---

## 0.1 Vocabulary (what the keywords mean)

- **Iteration** — a bounded unit of work with its own `PROMPT.md` (entry) and `REPORT.md` (exit), executed in its **own fresh session** that reads files, not chat history. Listed in `PLAN.md`.
- **"plan / scope / set up / do iteration N"** said *outside* N's execution session → **write `plan/iterations/N-<slug>/PROMPT.md`** so a fresh session can run it. Do **NOT** implement it inline in the current session. Scoping an iteration ≠ executing it.
- **"execute / run / continue iteration N"** → the kickoff in a fresh session; now you implement per its PROMPT.md.
- **Side session (§6)** — a quick fix/question with no PROMPT/REPORT.
- **In-iteration ad-hoc (§4.1)** — a small change handled inside the current iteration; recorded in its REPORT.

Default when ambiguous: treat "iteration" as the lifecycle above — scope a PROMPT, don't carry context across. If the user surfaces sizeable new work mid-session, prefer scoping it as an inserted/ad-hoc iteration PROMPT (§4.2, §5) over implementing inline.

---

## 1. The three documents

### 1.1 `DESIGN.md` — the spec

Numbered sections covering scope, constraints, data model, interfaces, end states, tone, non-goals.

**Body is mutable only via pivots.** Once a section is written and the project has moved past it, do not silently rewrite it. Instead, log the change in `PIVOTS.md` (see §1.2) and note which §-section is superseded.

### 1.2 `PIVOTS.md` — the decision pivot log (separate file)

A standalone, append-only, dated log of every meaningful design or plan change.

Each entry uses this template:

```markdown
### YYYY-MM-DD — <one-line title> (iter NN[.x], <when in the iter>)

- **Symptom / problem:** what surfaced, where, during which iteration. Include the playtest observation, perf number, or constraint that triggered it.
- **Decision:** what we are doing instead. Be specific — name files, sections, constants, mechanics.
- **Consequences:** what other parts of the design/code shift, what gets deleted, what becomes dead weight.
- **Supersedes:** `DESIGN.md §X`, `PLAN.md iter NN` — explicit list.
- **Rationale:** why this over the alternatives.
- **Status:** proposed | landed.
```

Order: newest at top. Older entries never edited; if a pivot is itself reversed, log a new entry that supersedes the old one.

Pivots come from two sources:
- **Iteration-driven** — a symptom surfaces mid-iteration; logged before the next session starts.
- **Ad-hoc** — user-driven mid-session change that is not tied to a planned iteration (see §4).

**PLAN.md vs PROMPT.md — why we copy, not link.** PLAN.md entries are short and stable (the canonical scope contract). PROMPT.md is much longer (typically 5–10× the PLAN.md entry) and carries the *executable* version: file paths discovered in prior iterations, grep commands, key counts, carry-over notes from REPORTs, pivot constraints still in force. Linking to PLAN.md would force the executing session to re-derive all of that. Copy-then-enrich is the right tradeoff.

### 1.3 `plan/PLAN.md` — the master iteration list

Numbered iterations (typical project: ~8–12). Each entry:

- **Title** (short noun phrase)
- **Goal** (one or two sentences)
- **In scope** (bullets)
- **Out of scope** (bullets — explicit non-work)
- **Acceptance criteria** (testable bullets)
- **Size** (S / M / L)
- **Dependencies** (prior iters)

Plus these blocks at the bottom:
- Templates for PROMPT / REPORT / CONTINUE / pivot entries ship with the klaus plugin (`skills/klaus/references/templates/`) — not duplicated here.
- Slug map

`PLAN.md` is **append-only for iteration entries**. Existing entries are not silently rewritten. If a planned iteration's scope changes, log a pivot and update the entry with a `Supersedes` reference.

Inserted iterations (decimal numbering: `04.a`, `04.b`, `10.a` …) are how the plan absorbs new work without renumbering everything that follows. See §4.

---

## 2. Per-iteration files

For every iteration `NN-<slug>`, the folder lives under `plan/iterations/NN-<slug>/`. Two files always; a third (`CONTINUE.md`) when the iteration is split across multiple sessions.

### 2.1 `PROMPT.md` — the entry contract

Written by the *previous* session. For iter 01, written by the Plan session (§9) after the user signs off on `PLAN.md`. Contains everything a fresh context needs to execute the iteration with no prior conversation.

```markdown
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
- testable bullets seeded from PLAN.md, optionally tightened with concrete checks (`grep -rn ... → empty`, `npx tsc --noEmit clean`, specific routes/screens to smoke-test).

## Deliverables for this session
1. Implement everything in "In scope".
2. Verify each acceptance criterion.
3. **Stop. Ask the user before writing REPORT.md and the next PROMPT.md** (see §3).
4. After sign-off: write `plan/iterations/NN-<slug>/REPORT.md`.
5. After sign-off: write `plan/iterations/<NN+1>-<next-slug>/PROMPT.md`.
6. Print only the short kickoff command for the next session as the final chat reply, in a code block:

   ```
   Execute iteration <NN+1>. Read plan/iterations/<NN+1>-<next-slug>/PROMPT.md and plan/PLAN.md, then follow the deliverables in PROMPT.md exactly.
   ```

## Constraints
- <project stack constraints, e.g. "TypeScript + Vite, no new global state, ...">
```

### 2.2 `REPORT.md` — the exit contract

Written at the end of the session, **after the user has signed off** on the implementation (see §3). Fixed template:

```markdown
# Iteration NN — <slug> — Report

## What was built
- bullets

## Files touched
- path/to/file.ext (new | modified)

## Deviations from plan
- "Originally said X, ended up doing Y because Z"  (empty if none)

## Ad-hoc work done in this session
- any user-requested changes outside the iteration's planned scope; link to the matching PIVOTS.md entry if one was logged

## Acceptance criteria status
- [x] / [ ] each criterion verbatim

## Open issues / TODOs spawned
- bullets

## Notes for next iteration
- anything the next session must know that isn't in DESIGN.md or PIVOTS.md
```

### 2.3 `CONTINUE.md` — mid-iteration handoff (optional)

Sometimes an iteration is too large to finish in one session — context runs out, focus drops, or the work is genuinely a multi-day piece. Instead of writing a premature REPORT or compressing quality to fit, **split the iteration across sessions** via a `CONTINUE.md` in the iteration's folder.

Not a failure mode. First-class tool.

**When to write CONTINUE.md** — the current session is ending but the iteration's PROMPT.md is not fully delivered. You have done some of the deliverables / acceptance bullets and want a fresh session to pick up the rest.

Template (lighter than PROMPT.md — the iteration's PROMPT.md is still the canonical scope; CONTINUE.md only records progress):

```markdown
# Iteration NN — <slug> — Continue (YYYY-MM-DD)

## Done so far
- deliverable / acceptance bullet, with file paths or commit refs
- ...

## Remaining
1. next concrete deliverable
2. ...

## Decisions taken mid-session
- anything that isn't in DESIGN/PIVOTS/PROMPT yet but the next session needs to know
- open questions for the user, if any
```

**Next session kickoff command:**

```
Continue iteration NN. Read plan/iterations/NN-<slug>/CONTINUE.md, plan/iterations/NN-<slug>/PROMPT.md, and plan/PLAN.md, then resume from "Remaining".
```

**On completion:** the *final* session of the iteration writes REPORT.md as usual. The CONTINUE.md file stays in the folder as historical record — do not delete. If a third session is needed, append a new dated block to CONTINUE.md (don't overwrite earlier ones).

---

## 3. The sign-off rule (the most important one)

**At the end of an iteration, do not write REPORT.md or the next PROMPT.md yet.**

When the implementation is done and verified, the assistant pauses and asks the user explicitly:

> "Iteration NN looks done — acceptance criteria green, [short verification note]. Want to review before I write REPORT.md and the next PROMPT.md? Any tweaks to land first?"

Reason: the user often has small changes they want before the iteration is sealed. If the REPORT is already written, every late change creates churn — the report has to be amended, the next PROMPT may need rewriting, and the file state diverges from what actually shipped.

Only after the user signs off:
1. Write `REPORT.md`.
2. Write the next iteration's `PROMPT.md`.
3. Print the kickoff command.

If the user requests changes during sign-off, treat them as in-scope, apply them, then ask again. The REPORT is the *last* artefact produced, never the first.

---

## 3.1 Acceptance tests — the verification contract

Manual verification does not accumulate. Verify a criterion by hand in iteration 6 and nothing protects it in iteration 13 — the user becomes the regression suite. Executable criteria fix that.

**Every acceptance criterion carries a tag, assigned when the PROMPT is scoped:**

- `auto` — machine-checkable. Becomes a test in the permanent suite.
- `manual` — genuinely not automatable. Must state **why** and give **exact re-runnable steps**.

Default to `auto`. `manual` is reserved for: hardware / OS-session events (calls, audio-route changes, Siri), OS-gated human gestures, physical-device install and signing, feel/perf judgement, and aesthetics. Anything scriptable on a simulator or in CI is `auto` — launch, tap, force-quit and relaunch, toggle network, assert DB rows, assert files on disk, assert logs.

**Order matters more than authorship.** `auto` tests are written *before* the implementation. A test written afterwards encodes what was built; a test written first encodes what was asked. Because the ordering does the work, the executor writes its own tests — no separate test-author agent, which would only add a cold context load and an API-contract negotiation across a boundary. Independence comes from the reviewer, which audits the tests instead of re-verifying everything by hand.

**The suite accumulates.** Every iteration runs all previous iterations' tests. Regressions surface for free, and the reviewer's effort stays flat while coverage compounds.

**Guards against the obvious failure mode** — an agent told "make it pass" will take the shortcut:

- The executor may never weaken a test to go green (no deleted assertions, loosened comparisons, `.skip`/`.only`, mocking out the thing under test, or hardcoded expected values). If a test is genuinely wrong, it says so out loud instead of editing quietly.
- The executor reports the **red run** — tests written, and that they failed for the right reason before implementation. A test that never failed proves nothing.
- The reviewer audits every new test against its criterion and, where cheap, mutation-checks it: break the behavior, confirm the test goes red, revert. A green suite with a hollow test is a FAIL.

**Spikes are exempt.** Discovery cannot be specified up front — forcing test-first onto a spike is waste. A `spike` iteration's output is written-down knowledge (README notes, a pivot entry); the next `feature` iteration turns that knowledge into `auto` criteria. Each PROMPT declares its kind: `feature` or `spike`.

**Bootstrap the harness in iteration 01** — the test runner, any end-to-end runner, and fixtures. "Built up from the beginning" only works if the first iteration can already write a test.

**What tests do not buy you.** They catch regressions and false "done" claims. They cannot catch a *wrong criterion* — a criterion faithfully implemented and faithfully tested can still be the wrong behavior, and the suite will be green about it. That is what phase 2 is for: the user using the thing. Tests protect the build; user verification protects the spec.

## 4. Ad-hoc iterations

Not every piece of work fits the planned grid. Mid-session, the user may surface something that wasn't in PLAN.md — a bug, a polish pass, a design exploration, a tooling setup.

**Two kinds of ad-hoc work:**

### 4.1 In-iteration ad-hoc (small, doesn't change the plan)

A change handled inside the current iteration session — e.g. a bug fix the user spots during verification, or a one-line tone tweak.

- Just do it.
- Record it in the iteration's REPORT under `Ad-hoc work done in this session`.
- No new PROMPT, no pivot — unless it changes the design or future iterations.

### 4.2 Standalone ad-hoc iteration (own session, own folder)

A piece of work big enough to warrant its own session but not part of the original plan — e.g. a tooling setup, a design exploration, a focused bug-hunt, a refactor.

- Create `plan/iterations/<NN.x>-<slug>/` — same folder as planned iterations. Ad-hoc work lives next to planned work; no separate `plan/adhoc/` directory. Use a decimal/letter suffix to slot it next to the iteration it relates to.
- Write a `PROMPT.md` (lighter — can be 5 lines: goal, scope, done-when).
- Write a `REPORT.md` at the end (lighter — what was done, files touched, anything to remember).
- If it changes design or future iterations: log a `PIVOTS.md` entry.
- Add an entry under PLAN.md so the iteration chain is still readable end-to-end.

Ad-hoc is encouraged, not a deviation. Dynamic redirection is the whole point — the plan is a working hypothesis, not a contract.

---

## 5. Inserted iterations (decimal numbering)

When a planned iteration reveals work that has to land *before* the next planned iteration, do not renumber the rest of the plan. Insert with a letter suffix:

- `04` → `04.a` → `04.b` → `05`
- `10` → `10.a` → `10.b` → `10.c` → `10.d` → `11`

Each inserted iteration:
1. Gets its own folder + PROMPT + REPORT.
2. Has a `PIVOTS.md` entry that explains *why* it was inserted and what existing PLAN.md/DESIGN.md sections it changes.
3. Is added to PLAN.md in the right slot with the same template as a planned iteration.

This is the visible fingerprint of a healthy iterative process: the chain bends without breaking.

---

## 6. Side sessions (no iteration, no REPORT)

Short, single-purpose sessions that don't deserve an iteration folder — e.g. a type-check fix, a settings tweak, a question. They write code and commits but no `PROMPT.md` / `REPORT.md`.

Rules:
- Fine to do them.
- If they produce a decision that affects the project, **log a `PIVOTS.md` entry**. That's how their output joins the main chain.
- If they uncover work, **add a PLAN.md entry** for it (planned or ad-hoc).

If a side session balloons into something with real scope, stop and promote it to an ad-hoc iteration (§4.2).

---

## 7. Conventions that carry the workflow

1. **One iteration = one session, unless explicitly split via `CONTINUE.md` (§2.3).** When context, focus, or scope overrun, write a CONTINUE.md and pick up in a fresh session — better than compressing quality. The iteration stays one logical unit; only the final session writes REPORT.md.
2. **The PROMPT.md is the entry contract.** Each iteration session opens with the kickoff command — no restating the goal in chat.
3. **The REPORT.md is the exit contract.** Mandatory template. `Deviations from plan` is non-optional.
4. **The next PROMPT.md is the handoff.** Last deliverable, written only after user sign-off (§3).
5. **PIVOTS.md is append-only and dated.** Never rewrite an entry; supersede it.
6. **DESIGN.md body is append-only past the point the project has moved past.** Mutations go to PIVOTS.md.
7. **PLAN.md is append-only for iteration entries.** Inserted iterations use decimals (§5).
8. **Side sessions don't get REPORTs**, but they can produce PIVOTS.md entries or new PLAN.md entries.
9. **State lives in files, not in chat.** A new session should be able to resume the project knowing only DESIGN.md + PIVOTS.md + the latest REPORT.md + its own PROMPT.md.
10. **The final chat reply of an iteration session is the kickoff command for the next one.** Nothing else.

---

## 8. What this workflow optimises for

- **Context discipline.** Sessions stay small because they don't carry conversation history forward — they carry files.
- **Reversible decisions stay reversible.** Every pivot is dated and explicit about what it supersedes.
- **Mid-flight redirection is cheap.** Inserted iterations + ad-hoc iterations + side sessions are all first-class.
- **The plan stays trustworthy.** Nothing is silently amended.
- **User stays in control.** No artefact is sealed before sign-off; the user can always steer at the end of an iteration without paying churn cost.

---

## 9. Starting a new project from this file

Session 1 (Design):
- Read the brief / idea source; interview if none.
- **UI-bearing project:** explore **≥4 distinct UX-structure directions in parallel** (one subagent each, plain HTML-only wireframes — structure/navigation/where-actions-live, not looks), wired together by a `mock/index.html` chooser. User picks one (or a blend); the winner is promoted to `mock/` root (overview `index.html` + one page per screen, linked to walk the flow). Stop, get sign-off. Skip this step for CLI/library/backend projects with no visual surface.
- Produce `DESIGN.md`, drafted *from the approved mock* — the interfaces/end-states sections reference the mock's screen files. The mock is a frozen origin snapshot; DESIGN.md + PIVOTS.md are the source of truth once the project moves past it.
- Initialise empty `PIVOTS.md` with a header.
- Stop. Ask user to review before writing PLAN.md.

Session 2 (Plan):
- Read `DESIGN.md`.
- Produce `plan/PLAN.md` with numbered iterations + templates + slug map.
- Stop. Ask user to review and sign off on PLAN.md before writing the first PROMPT.
- After sign-off: write `plan/iterations/01-<first-slug>/PROMPT.md`.
- Print the kickoff command for iter 01.

Session 3+ (Iterations):
- Follow §2 + §3 every time.
