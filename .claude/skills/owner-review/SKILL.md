---
name: owner-review
description: Walk the owner through awaiting_owner_review.md interactively in poker-docs — accept/amend ADRs, confirm edits, answer open questions — and apply each resolution to the vault immediately. Use when the owner says "let's review", "what's waiting on me", "clear my queue", or asks to go through the review queue.
---

# owner-review

The owner is present and answering. Your job is to make every item in `awaiting_owner_review.md` resolvable **from this conversation** and to write each answer into the vault the moment it is given. One item at a time, nothing skipped silently, nothing guessed.

## 1. prepare (read-only)

1. Read `awaiting_owner_review.md`.
2. Find items that should be there but aren't, and add them before starting (they are the same kind of blocker):
   - `03_decisions/` with `status: proposed` → *decisions to accept or amend*
   - `_draft_` / `_draft —` markers in `vision.md` → *edits to confirm*
   - `## open questions` bullets in `02_initiatives/ongoing/` notes that are `active`, `blocked`, or `proposed` with `p0`/`p1` → *questions to answer*
   - files in `00_inbox/` (ignore `.gitkeep`) → refresh the count under *inbox to triage*
3. For every item, read the linked note so you can present it without the owner opening anything: the ADR's options and recommendation, the draft text, the question plus whatever context the note gives.
4. Open with a two-line summary: counts per section and how long it will take at one prompt per item. Ask whether to go top-down or start with a section. If the owner names a section or item, start there.

## 2. walk the queue

Present items with `AskUserQuestion` wherever the answer is a choice; group up to four questions from the **same linked note** into one prompt so the owner isn't clicked to death. Free-text answers come through "Other". Every prompt offers **skip** (leave in queue) and the owner can say "stop" at any point — then jump to step 4.

**decisions to accept or amend** — show context → options → recommendation in a few lines. Options: *accept* / *accept with amendments* / *reject* / *skip*.
- accept → set `status: accepted` in the ADR. Update `status.md` *decisions* (move from proposed to accepted), the driving initiative's `## decisions` line, and `roadmap.md` if the ADR changes ordering or milestones.
- amend → collect the amendments, edit the ADR **while still `proposed`**, show the diff-level summary, then ask accept/skip again. Never edit a body after it's `accepted`.
- reject → set `status: rejected`, add a `## rejected because` line with the owner's reason, and leave the initiative pointing at it as rejected.
- If the ADR touches randomness, cryptography, custody or the fairness protocol and `fairness-reviewer` has not been run on this version, say so before the owner accepts and offer to run it first.

**edits to confirm** — quote the draft text verbatim. Options: *confirm* / *rewrite* / *strike* / *skip*.
- confirm → remove the `_draft_` marker and any "to be confirmed" phrasing; keep the text.
- rewrite → take the owner's wording (ask for it if not given), replace the draft, remove the marker.
- strike → remove the draft text; if the section is then empty, leave a one-line placeholder and add a question to the queue.
- `vision.md` changes only ever happen here or on explicit owner instruction.

**questions to answer** — state the question, the note's context, and a suggested default only if the note or research supports one (label it *suggestion*, not fact). Options: concrete choices when they exist (e.g. `6` / `9` seats), otherwise free text.
- Record the answer in the linked note where it belongs: a `scope` line, a `done when` criterion, a task, or a spec requirement — not just the log. Remove the bullet from `## open questions`. Add a dated `## log` line quoting the answer.
- If the answer is a choice that `CLAUDE.md` says needs an ADR (randomness, crypto, chain, custody, jurisdiction, server model), don't bury it in an initiative: draft it via `/new-decision` and put the new ADR back in the queue under *decisions*.
- If the owner answers "don't know yet" or "decide later", keep the item and append `(deferred YYYY-MM-DD: reason)`.

**inbox to triage** — if the count is non-zero, offer to run `/triage-inbox` now; otherwise leave the line.

After **each** resolved item, before moving on: apply the vault edit, tick the line in `awaiting_owner_review.md`, and move it to *resolved recently* as `- [x] … — resolved YYYY-MM-DD: <one-word outcome>`. Keep the last ~10 there, drop older ones. Don't batch this to the end — a stopped session must leave the queue truthful.

## 3. handle what comes up

- If an answer contradicts an accepted ADR or `vision.md`, say so before applying it and ask which one wins. A reversal of an accepted ADR is a superseding ADR (`/new-decision`), not an edit.
- If an answer spawns new questions, add them to the queue under the right section with today's date and tell the owner — don't ask them now unless they block the current item.
- If an answer is a fairness claim, it stays labelled **assumption** until a spec in `04_specs/` backs it.
- Never delete queue items; ticked items move to *resolved recently*, unresolved ones stay.

## 4. close

1. Bump `updated:` in `awaiting_owner_review.md`.
2. `status.md`: refresh *decisions*, *next actions* and *open questions* to reflect what was resolved; bump `updated:`.
3. Append one line to today's daily note under `## 🤖 ai sessions`: how many items resolved, which ADRs changed status, what remains.
4. Report in ≤6 bullets: resolved (with links), deferred, newly added to the queue, and the single next step. Do not commit unless asked.
