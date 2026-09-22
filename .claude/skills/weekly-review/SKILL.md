---
name: weekly-review
description: Run the weekly review for poker-docs — summarise the week, check honesty of status/roadmap, and set next week's focus. Use when the user asks for a weekly review or at end of week.
---

# weekly-review

1. Note file: `01_daily/weekly/gggg-[w]ww.md` for the current ISO week (e.g. `2026-w39.md`). Create from `_templates/weekly_review.md` if missing.
2. Gather the week's facts: daily notes for the week, initiative `## log` lines dated this week, ADRs created/accepted this week, and `git log --since="1 week ago" --oneline` in this vault and in `../poker-monorepo` (if it has commits).
3. Fill the note: **shipped**, **slipped** (with why), **initiatives status** (one line each: on track / at risk / blocked), **decisions**, **risks & open questions**.
4. Run `/triage-inbox`, dispatch the `vault-auditor` subagent, and check the hygiene checkboxes honestly against its report — do not tick `status.md matches reality` without actually comparing.
5. Reconcile `roadmap.md` (now/next/later/done, milestone table) and `status.md` with reality; flag milestones that look at risk.
6. Propose **next week focus** (≤3 items) and ask the user to confirm or change it before writing it.

Be candid: name slippage and unresolved risks plainly.
