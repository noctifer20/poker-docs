---
type: review_queue
updated: 2026-09-23
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
- [ ] [[v1_0_casual_multiplayer_poker]] → **top-up reading**: your "Correct. No top ups." was read as *between hands only, up to 100bb, none for stacks ≥ 100bb*. Confirm or correct. Added 2026-09-23.
- [ ] [[v1_0_casual_multiplayer_poker]] → **sit-out grace period**: you asked for a "reasonable" time before a sitting-out player is removed. Proposed default: the same ~2 minutes as the disconnect hold, one rule for both. Confirm or give a number. Added 2026-09-23.
- [ ] [[v1_0_casual_multiplayer_poker]] → **banned-word list**: your "No need to skip this one" was read as *no list needed; keep the manual copy review*. If you meant the opposite, say so and it becomes a task. Added 2026-09-23.

## inbox to triage
<!-- count only; run /triage-inbox -->
- `00_inbox/` is empty.

## resolved recently
<!-- move ticked items here with the date, keep the last ~10, then drop -->
- [x] [[0003_monorepo_structure_and_tech_stack]] — resolved 2026-09-23: accepted
- [x] [[0002_staged_delivery_free_play_first]] — resolved 2026-09-22: accepted
- [x] [[vision]] — *why this exists*, *who it's for*, *path*, *non-goals* — resolved 2026-09-22: confirmed
- [x] [[v1_0_casual_multiplayer_poker]] — seats, blinds/stack/top-up, timer — resolved 2026-09-22: answered
- [x] [[v1_0_casual_multiplayer_poker]] — disconnects, spectators, hosting — resolved 2026-09-22: answered
- [x] [[v1_1_private_lobbies_for_friends]] — host settings, host powers, lobby lifetime — resolved 2026-09-22: answered
