---
name: design
description: Session 1 of the klaus workflow — explore ≥4 UX-structure directions in parallel as clickable HTML wireframes (Tailwind Play CDN, shared house rules, one subagent each, structure not looks), let the user pick, then draft DESIGN.md from the chosen mock and initialise PIVOTS.md. Interviews the user if no brief exists. Mock step is skipped for projects with no user-facing surface.
disable-model-invocation: true
---

# /klaus:design — design pass (mock → spec)

One command, two phases with a sign-off gate between them:

1. **Mock** — a throwaway, clickable **HTML-only** mock of every screen. Settle look, screens, flow, and interaction model here, where changing them costs minutes.
2. **Design** — write `DESIGN.md` *from the approved mock*, plus an empty `PIVOTS.md`.

Produce `mock/`, `DESIGN.md`, `PIVOTS.md`. Nothing else — no PLAN.md, no app code.

## Phase 0 — Find the brief

Sources, in order: a file passed as argument, an obvious brief in the repo (`BRIEF.md`, `IDEA.md`, README section), or the idea in the user's message.

**No brief → interview mode.** Ask the user to explain the idea in their own words. Then dig — one focused round at a time, not a wall of questions:
- What is it, for whom, and what does "done" look like?
- Hard constraints: stack, platform, deadline, must-use / must-avoid.
- Data model and interfaces at whatever fidelity the user can give.
- **The screens:** what are they, what's on each, how does the user move between them, what's the one primary action per screen. (This feeds the mock directly.)
- Tone / feel.
- **Non-goals** — push explicitly: "what are we NOT building?"

Stop interviewing when you can both draw every screen and write every DESIGN.md section without inventing content.

**Is this project UI-bearing?** If it has a user-facing surface (app, site, tool with screens) → do Phase 1. If it's a CLI, library, or backend with no visual surface → **skip Phase 1**, tell the user why, go straight to Phase 2. If unclear, ask.

## Phase 1 — Mock (skip if not UI-bearing)

Don't build one mock — **explore several directions in parallel, then let the user pick.** This is the cheapest place to diverge.

**Variants differ in UX structure, not in looks.** What varies is *how the product is organized*, not how it's styled. Looks (brand, color, polish) are deliberately deferred — they're settled later, when the real screens are built.

But *plain* must not mean *unreadable*. A mock the user can't parse can't be judged. Every variant therefore renders through **Tailwind's Play CDN with stock defaults** — same utilities, same default palette and type scale for everyone. The house rules below are guardrails on *consistency*, not a component kit: the agent still composes each screen however its structural approach demands. Two effects — each screen is legible on its own, and the variants stay comparable, because nothing but structure varies.

Axes a variant can differ on:
- **Navigation model** — tab bar vs drill-down stack vs one scrolling hub vs gesture-driven.
- **Screen decomposition** — many focused screens vs few dense screens; what merges, what splits.
- **Where the primary action lives & how it fires** — per-row buttons vs tap-to-act vs swipe vs long-press vs a detail screen.
- **Persistent surfaces** — always-visible bar (e.g. a mini-player) vs a dedicated screen vs a modal.
- **Information hierarchy** — what's grouped, what's surfaced first, what's a level down.

1. **Derive the surface.** From Phase 0, list every screen/view and its one primary action.

2. **Fix the house rules** (below) for this project before spawning anything — frame width (mobile ≈ `max-w-[420px]`, desktop ≈ `max-w-6xl`) and, if the product is genuinely dark-surfaced, the neutral direction. Every subagent gets the same list, verbatim.

3. **Pick ≥4 distinct structural approaches** and name them by their UX shape — e.g. *"tabs + dedicated player screen"*, *"single feed + persistent mini-player bar, tap-to-act rows"*, *"drill-down stack, actions in a detail view"*, *"one hub, swipe actions"*. Each is a different way to organize the **same** screens and actions.

4. **Spawn one subagent per approach, in parallel** (`general-purpose`, background). Each gets: the brief, the screen/action list, its assigned structural approach, and the mock rules + house rules below. Each builds a **complete, clickable** wireframe of *all* screens in its own folder `mock/<variant-slug>/` (own `index.html` entry + one `<screen>.html` per screen, internal links so the whole flow is walkable). Each returns the path to its entry + a one-line description of its structural approach and the key trade-off it makes.

5. **Write the chooser: `mock/index.html`** — a gallery that **wires the variants together**: each variant as a card (structural name + one-line approach/trade-off + link to its `mock/<variant-slug>/index.html`). Same house rules apply. This is the compare surface.

Mock rules (given to every subagent):
- **Static HTML + Tailwind Play CDN.** Every page is self-contained and opens by double-click:
  ```html
  <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
  ```
  No build step, no framework, no real data. (Play CDN is dev-only by design — fine, the mock never ships. It does need network on first open; say so if the user is offline.)
- **Legible, not decorated.** Every screen has a real title, real section labels, and **plausible placeholder content** — actual-looking names, titles, timestamps, counts. No `Lorem ipsum`, no `[box]`, no unlabelled rectangles. The user must be able to tell what a screen shows without asking.
- **Annotate what a static page can't show** — a small dashed callout for interactions like "swipe left to archive", "long-press → multi-select".
- **Structure, not looks.** No visual-design or brand work, no `frontend-design` skill. Anything that draws the eye to *style* is a distraction from judging *structure*.
- **Throwaway reference, not app code.** Never uses the project's real stack, never ships. Its job: settle the UX structure, then serve as the structural spec for the UI iterations.

House rules — the consistency guardrails. Same for every variant, no per-variant deviation:
- **Stock Tailwind only.** Default palette, default spacing and type scale. No `@theme` overrides, no custom CSS, no web fonts, no icon libraries, no images. If a utility doesn't exist, that's a hint the mock is drifting into design work.
- **All separation comes from the neutral scale, not colour** — surfaces (`bg-white` on a `bg-neutral-100` page), edges (`border-neutral-200`), three text levels (`text-neutral-900` / `-600` / `-400`). Colour is spent entirely on the accent below.
- **Exactly one accent colour, project-wide** (pick one, e.g. `blue-600`), and it only ever marks the **one primary action per screen**. The accent *is* the structural signal — where the action lives. Nothing else may borrow it.
- **No decoration:** no gradients, no shadows beyond `shadow-sm`, no hero art, no rounded-full pill soup. `rounded-lg` and a border is the house look.
- Everything else — layout, composition, density, how a row or bar or sheet is built — is the agent's call. The approach it was assigned decides that, not this list.

**Stop. Sign-off gate.** Ask the user to open `mock/index.html`, walk each variant, and pick:

> "Four structural directions are ready — open `mock/index.html` and click through each. These differ in how the app is organized (navigation, where actions live, persistent surfaces), not in looks. Pick one, or blend them (e.g. A's navigation + C's player). Then I'll make that the canonical mock and draft DESIGN.md from it."

On the pick (a single variant or a stated blend):
- **Promote the choice to `mock/` root** — flatten the winning screens to `mock/<screen>.html` and rewrite `mock/index.html` as the plain overview (every screen linked, walkable). Fix the internal links. Apply any blend the user asked for — a blend is safe precisely because both parents follow the same house rules.
- Remove the losing variant folders (or move to `mock/explorations/` only if the user wants them kept).

Apply any change requests to the chosen mock, ask again. Repeat freely — cheap here. Only move to Phase 2 on explicit sign-off of the canonical `mock/`.

## Phase 2 — Design (after mock sign-off)

Read the approved mock. Write **`DESIGN.md`** — numbered §-sections covering: scope, constraints, data model, interfaces, end states, tone, non-goals. Sections are referenced later as `DESIGN.md §X` by pivots — number them stably. Mark open questions inline rather than guessing.

- The **interfaces** and **end-states** sections describe the mock's screens and **reference their files** (e.g. "Player — see `mock/player.html`"). The mock is the picture; DESIGN.md is the words.
- Add a short note in DESIGN.md: *the mock is a frozen origin snapshot. Once the project moves past it, DESIGN.md + PIVOTS.md are the source of truth — the mock is not re-rendered on every pivot.*

Initialise **`PIVOTS.md`** with only a header:

```markdown
# Pivots — decision log

Append-only, dated, newest on top. Every meaningful design or plan change lands here. Entries are never edited; a reversed pivot gets a new superseding entry.
```

**Stop.** Ask the user to review DESIGN.md before any planning. Do not write PLAN.md — that is `/klaus:plan`, a fresh session.

## Rules

- The design describes the *what*, not the build order.
- Mock before words for UI projects: clickable wireframes catch **UX-structure** decisions (navigation, screen decomposition, where actions live, persistent surfaces) at design time, not after a reviewer-passed screen is built. Looks are explicitly out of scope here — they're settled later when the real screens are built.
- Plain is the goal; illegible is a bug. If the user can't tell what a screen is showing, the mock has failed at its only job.
- House rules are guardrails, not a component kit. They pin the things that must not vary between variants (palette, scale, where the accent goes) and leave composition to the agent.
- If the user's idea shifts during Phase 0/1, that's normal — the mock and DESIGN.md reflect the final shape; no pivot entries yet (the log starts at the first post-review change).
- Mock is HTML-only and throwaway. Never wire it to the real stack or ship it.
