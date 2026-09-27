---
type: review_queue
updated: 2026-09-26
tags: [review]
---

# awaiting owner review

> Everything that is blocked on **you** (the owner), in one place. AI agents add items here whenever something needs your decision, confirmation or answer; you resolve an item by acting on it (accepting the ADR, editing the note, answering the question) and moving the line to *resolved recently* — or run `/owner-review` to be walked through the queue and have each answer applied for you. Empty sections stay in place. Items link to where the real content lives — this file is a queue, not a copy.

## decisions to accept or amend
<!-- ADRs in 03_decisions/ with status: proposed. Only you flip proposed → accepted. -->

## edits to confirm
<!-- notes that need your approval to change (vision.md) or where an agent wrote something in your name -->
- [ ] [[vision]] → *success looks like* — placeholder only, needs your definition. Added 2026-09-22. (deferred 2026-09-22: skipped in review) (deferred 2026-09-25: skipped in review)

## questions to answer
<!-- open questions agents must not guess at. Answer in the linked note (or reply in chat) and remove the line. -->
- [ ] [[nlhe_cash_game_rules]] R42 — leaving mid-hand: spec says treat like a disconnect (check if free, else fold, on your turns); the server currently **always folds** a leaver (my brief, not a decision). Which? Added 2026-09-26. The new web leave-sheet copy follows the server (fold + seat released); it changes with your answer.
- [ ] [[nlhe_cash_game_rules]] R16.1 — heads-up, previous big blind leaves, newcomer sits between the empty seat and the remaining player: newcomer takes the BB (remaining player posts SB twice running — current engine), or always give the continuing player the BB? Added 2026-09-26.
- [ ] Root `package.json` description and README first line say "provably random poker game" — keep as the project's goal statement, or reword until v3.0? Added 2026-09-26.

## inbox to triage
<!-- count only; run /triage-inbox -->
- `00_inbox/` has 1 note ([[code_docs_verification_2026_09_26]], 2026-09-26). Run `/triage-inbox`.

## resolved recently
<!-- move ticked items here with the date, keep the last ~10, then drop -->
- [x] `fix/heads-up-transition-bb` merge — resolved 2026-09-26: merged (`ff424a5`)
- [x] `feat/web-table` merge — resolved 2026-09-26: merged (`15c2856`)
- [x] [[design_system_ground_rules]] — nickname limit 16 — resolved 2026-09-25: confirmed
- [x] [[ui_design_system]] — landscape phones — resolved 2026-09-25: portrait-only
- [x] [[design_system_ground_rules]] — UI-R14 at 320px — resolved 2026-09-25: accepted
- [x] [[v1_0_casual_multiplayer_poker]] — top-up reading — resolved 2026-09-25: confirmed
- [x] [[v1_0_casual_multiplayer_poker]] — sit-out grace period — resolved 2026-09-25: 2 minutes
- [x] [[v1_0_casual_multiplayer_poker]] — banned-word list — resolved 2026-09-25: none
- [x] [[nlhe_cash_game_rules]] — R8 uncontested wins — resolved 2026-09-25: hidden
- [x] [[nlhe_cash_game_rules]] — R23 cumulative short all-ins — resolved 2026-09-25: TDA
- [x] [[nlhe_cash_game_rules]] — top-up opt-in vs automatic — resolved 2026-09-25: opt-in
- [x] [[0004_design_system_in_code]] — resolved 2026-09-24: accepted
