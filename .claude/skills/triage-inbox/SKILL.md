---
name: triage-inbox
description: Sweep 00_inbox in poker-docs and file each capture into the right place. Use when the user asks to triage/clean the inbox or during the weekly review.
---

# triage-inbox

For each note in `00_inbox/` (ignore `.gitkeep`), decide one destination:

| it is… | goes to |
|---|---|
| a task with no home | a checkbox in `backlog.md` (or the relevant initiative) |
| outcome-sized work | `/new-initiative` |
| a choice to be made | `/new-decision` |
| external knowledge, a link, a paper | `05_research/` (from `research.md`; add sources) |
| a design or protocol idea | `04_specs/` (from `spec.md`, `status: draft`) |
| a new domain term | `glossary.md` |
| a vision/scope thought | proposal against `vision.md` (don't edit it; ask) |
| day-log material | today's daily note |
| junk / duplicate | propose deletion |

Rules:
- Show the user the proposed routing as a short table **before** moving anything; then apply after they confirm.
- Never delete a note without confirmation. Move (don't copy) so backlinks update.
- Set proper frontmatter for the destination type and fix the filename to the naming convention.

End with the inbox count (target: 0).
