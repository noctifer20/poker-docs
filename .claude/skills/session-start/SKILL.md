---
name: session-start
description: Load current project context at the start of a working session in poker-docs. Use when the user starts work, asks "where are we", "what's next", or "catch me up".
---

# session-start

Get oriented, then report briefly. Read-only — change nothing.

1. Read `status.md`, `awaiting_owner_review.md`, `roadmap.md` (now/next), and `vision.md` if `status.md` mentions scope changes.
2. List `02_initiatives/ongoing/`; read every note whose frontmatter `status` is `active` or `blocked`, plus `proposed` ones with `priority: p0` or `p1`.
3. Read the newest note in `01_daily/` (not `weekly/`) and count the files in `00_inbox/` (ignore `.gitkeep`).
4. List `03_decisions/` entries with `status: proposed` and check each is listed in `awaiting_owner_review.md`; note any that aren't.
5. Check for drift: does `status.md` match the initiatives' actual state and the latest daily log? Note any mismatch.

Report (short, no preamble):
- **phase & focus** — one line
- **active / blocked initiatives** — name, priority, next unchecked task
- **waiting on you** — item counts per section of `awaiting_owner_review.md`, plus anything missing from it; if non-empty, suggest `/owner-review`
- **drift or risks** — only if found
- **suggested next step** — one concrete recommendation

Then ask what to work on, or proceed if the user already said.
