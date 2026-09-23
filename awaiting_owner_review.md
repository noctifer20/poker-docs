---
type: review_queue
updated: 2026-09-24
tags: [review]
---

# awaiting owner review

> Everything that is blocked on **you** (the owner), in one place. AI agents add items here whenever something needs your decision, confirmation or answer; you resolve an item by acting on it (accepting the ADR, editing the note, answering the question) and moving the line to *resolved recently* — or run `/owner-review` to be walked through the queue and have each answer applied for you. Empty sections stay in place. Items link to where the real content lives — this file is a queue, not a copy.

## decisions to accept or amend
<!-- ADRs in 03_decisions/ with status: proposed. Only you flip proposed → accepted. -->

## edits to confirm
<!-- notes that need your approval to change (vision.md) or where an agent wrote something in your name -->
- [ ] [[vision]] → *success looks like* — placeholder only, needs your definition. Added 2026-09-22. (deferred 2026-09-22: skipped in review)

## questions to answer
<!-- open questions agents must not guess at. Answer in the linked note (or reply in chat) and remove the line. -->
- [ ] [[nlhe_cash_game_rules]] → **R8 and uncontested wins**: R8 says any pot winner must reveal their hole cards. Does that apply when everyone else folds (no showdown)? Standard poker: no — the winner may keep cards hidden. The Claude Design screen S09b assumes hidden. Confirm. Added 2026-09-23.
- [ ] [[design_system_ground_rules]] → **nickname limit 16 characters** (UI-R15) is a design proposal — confirm or give a number. Added 2026-09-23.
- [ ] [[v1_0_casual_multiplayer_poker]] → **top-up reading**: your "Correct. No top ups." was read as *between hands only, up to 100bb, none for stacks ≥ 100bb*. Confirm or correct. Added 2026-09-23.
- [ ] [[v1_0_casual_multiplayer_poker]] → **sit-out grace period**: you asked for a "reasonable" time before a sitting-out player is removed. Proposed default: the same ~2 minutes as the disconnect hold, one rule for both. Confirm or give a number. Added 2026-09-23.
- [ ] [[v1_0_casual_multiplayer_poker]] → **banned-word list**: your "No need to skip this one" was read as *no list needed; keep the manual copy review*. If you meant the opposite, say so and it becomes a task. Added 2026-09-23.
- [ ] [[nlhe_cash_game_rules]] → **top-up: opt-in or automatic?** Your answers confirmed *what* the free top-up does (between hands, up to 100bb, none if already ≥100bb) but not *whether the player has to request it*. The spec assumes opt-in (player-initiated, matches "may top up" and standard cash-game convention) as an engine default — confirm or correct. Added 2026-09-23.

## inbox to triage
<!-- count only; run /triage-inbox -->
- `00_inbox/` is empty.

## resolved recently
<!-- move ticked items here with the date, keep the last ~10, then drop -->
- [x] [[0004_design_system_in_code]] — resolved 2026-09-24: accepted
- [x] [[design_system_ground_rules]] — round-4 fixes — resolved 2026-09-24: not sent; owner froze the design, gaps fixed in implementation (spec → *known gaps*)
- [x] [[design_system_ground_rules]] — round-3 fixes — resolved 2026-09-23: already implemented in Claude Design (version of 2026-09-23 07:30 UTC); spot-check running 2026-09-24
- [x] [[ui_design_system]] — signature element — resolved 2026-09-23: A · pointer tab
- [x] [[design_system_ground_rules]] — round-2 fixes pasted into Claude Design — resolved 2026-09-23: done, verified by `design-reviewer`
- [x] [[design_system_ground_rules]] — large text — resolved 2026-09-23: add tap-a-seat sheet; bar covering lower seats at 200% accepted
- [x] [[design_system_ground_rules]] — paste the brief into Claude Design — resolved 2026-09-23: done → [Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy)
- [x] [[ui_design_system]] — visual direction — resolved 2026-09-23: C · Modern Felt, sharpened; four-colour deck default-on; dark only for v1.0
- [x] [[0003_monorepo_structure_and_tech_stack]] — resolved 2026-09-23: accepted
- [x] [[0002_staged_delivery_free_play_first]] — resolved 2026-09-22: accepted
- [x] [[vision]] — *why this exists*, *who it's for*, *path*, *non-goals* — resolved 2026-09-22: confirmed
- [x] [[v1_0_casual_multiplayer_poker]] — seats, blinds/stack/top-up, timer — resolved 2026-09-22: answered
- [x] [[v1_0_casual_multiplayer_poker]] — disconnects, spectators, hosting — resolved 2026-09-22: answered
- [x] [[v1_1_private_lobbies_for_friends]] — host settings, host powers, lobby lifetime — resolved 2026-09-22: answered
