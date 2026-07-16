---
name: status
description: Summarise where a klaus project stands — current iteration, done vs remaining, open TODOs, binding pivots, any unfinished CONTINUE.md.
disable-model-invocation: true
---

# /klaus:status — where are we

Read-only. No files written, nothing implemented.

## Read

1. `plan/PLAN.md` — the full iteration list.
2. `plan/iterations/*/REPORT.md` — which iterations are sealed; the latest REPORT in full (open issues, notes for next).
3. Any `CONTINUE.md` without a sibling REPORT.md — an iteration in flight.
4. `PIVOTS.md` — entries still binding on upcoming work (status `proposed`, or `landed` ones superseding not-yet-executed iterations).
5. Folders with PROMPT.md but no REPORT.md — scoped but not executed.

## Report (chat only)

- **Now:** last sealed iteration / in-flight iteration with its remaining items.
- **Next:** the next kickoff command, ready to paste.
- **Plan health:** N of M sealed, inserted iterations so far, S/M/L remaining.
- **Carry-over:** open issues + notes-for-next from the latest REPORT.
- **Binding pivots:** one line each.
- **Anomalies:** missing REPORTs, PROMPT-less next iteration, stale CONTINUE.md — anything that breaks the chain.
