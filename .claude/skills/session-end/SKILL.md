---
name: session-end
description: Wrap up a working session in poker-docs — write the daily log line, refresh status.md, and update initiatives/tasks. Use when the user says they're done, wrapping up, or asks to "update the docs/status".
---

# session-end

Leave the vault accurate. Work from what actually happened in this session (conversation + files changed), not from memory of intentions.

1. **Daily note** — open `01_daily/<today>.md` (create from `_templates/daily_note.md` if missing). Under `## 🤖 ai sessions` add one line: what was asked, what changed (link the notes), what's left.
2. **Initiatives** — for each initiative touched: tick finished tasks, add newly discovered tasks, append a dated line to `## log`, and adjust `status` if it changed (`active`/`blocked`/`done`/`dropped`). If it is now `done` or `dropped`, move it to `02_initiatives/past/` and update `roadmap.md`.
3. **Loose tasks** — anything found that fits no initiative goes to `backlog.md`.
4. **Decisions** — if a significant choice was made or implied, make sure an ADR exists (`/new-decision`); never mark one `accepted` yourself.
5. **awaiting_owner_review.md** — add every new item that needs the owner's decision, confirmation or answer (link + date, under the right section); move items the owner resolved this session to *resolved recently*; refresh the inbox count and `updated:`.
6. **status.md** — rewrite (don't append): bump `updated:`, then refresh *phase*, *current focus*, *recent changes* (keep the last ~5), *decisions*, *blockers*, *risks*, *next actions*, *open questions*. Remove anything no longer true.
7. **Code work** — if `../poker-monorepo` changed, note the relevant commit/PR/path in the initiative log; don't paste code.

Finish by summarising in ≤5 bullets what you updated. Do not commit unless asked.
