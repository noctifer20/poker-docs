---
type: review_queue
updated: 2026-09-29
tags: [review]
---

# awaiting owner review

> Everything that is blocked on **you** (the owner), in one place. AI agents add items here whenever something needs your decision, confirmation or answer; you resolve an item by acting on it (accepting the ADR, editing the note, answering the question) and moving the line to *resolved recently* — or run `/owner-review` to be walked through the queue and have each answer applied for you. Empty sections stay in place. Items link to where the real content lives — this file is a queue, not a copy.

## decisions to accept or amend
<!-- ADRs in 03_decisions/ with status: proposed. Only you flip proposed → accepted. -->
- [ ] [[0006_dev_review_environment]] — the `dev` branch / poker-dev environment you asked for on 2026-09-29, now **live**. Your direction is in it as given; I chose the details: checks must pass before a deploy, a timer on the VPS triggers Dokploy (the panel stays tailnet-only), feature branches (not `dev`) merged into `main`, rejected changes reverted on `dev`. Accept, or amend any of those. Added 2026-09-29.

## edits to confirm
<!-- notes that need your approval to change (vision.md) or where an agent wrote something in your name -->
- [ ] [[vision]] → *success looks like* — placeholder only, needs your definition. Added 2026-09-22. (deferred 2026-09-22: skipped in review) (deferred 2026-09-25: skipped in review)

## questions to answer
<!-- open questions agents must not guess at. Answer in the linked note (or reply in chat) and remove the line. -->
- [ ] [[nlhe_cash_game_rules]] R42 — leaving mid-hand: spec says treat like a disconnect (check if free, else fold, on your turns); the server currently **always folds** a leaver (my brief, not a decision). Which? Added 2026-09-26. The new web leave-sheet copy follows the server (fold + seat released); it changes with your answer.
- [ ] [[nlhe_cash_game_rules]] R16.1 — heads-up, previous big blind leaves, newcomer sits between the empty seat and the remaining player: newcomer takes the BB (remaining player posts SB twice running — current engine), or always give the continuing player the BB? Added 2026-09-26.
- [ ] Root `package.json` description and README first line say "provably random poker game" — keep as the project's goal statement, or reword until v3.0? Added 2026-09-26.
- [ ] [[playtest_feedback_2026_09_28]] — **how to make the visual changes** (seat vs felt colours, card size). You said on 2026-09-28 it is "both design and not design". Recommendation: split it. Motion, pacing, highlight and sound are behaviour — build them in code under UI-R25/R37/R38, no design round. Seat colours and card size are look — change them in code too (the repo is canonical per [[0004_design_system_in_code]]), but show you before/after screenshots for approval before merging. Claude Design stays frozen as the v1.0 reference. OK, or do you want a Claude Design round? Added 2026-09-28.
- [ ] `feat/playtest-feedback-1` **on poker-dev now** (merged into `dev` 2026-09-29 as `315852b`, parts A and B together) — try it on https://poker-dev.noctifer20.com on your phones. Part A (pacing, motion, highlight, sound + vibration): approve for `main`? Part B is the item below. Added 2026-09-28; updated 2026-09-29. See [[ui_design_system]] log.
- [ ] **Pacing durations** — starting values in `packages/protocol/src/pacing.ts` (bet/call/raise 1.2s, check/fold 0.9s, each hand shown 1.3s, each pot awarded 1.8s; a heads-up hand to showdown takes ~30–35s). Right, too slow, too fast? Should your own action be held as long as others'? Should automatic folds of away seats be shown at all? Added 2026-09-28.
- [ ] **Sound & vibration** (UI-R38) — confirm the event list (sound: deal, check, bet/call/raise, fold, your turn, win, timer ≤10s; vibration: your turn, timer ≤10s), the two off switches, and whether "win" sounds for every pot or only yours. Vibration **cannot work on iPhone** — accept Android-only? Added 2026-09-28.
- [ ] **Seat colours proposal** (`3236337`) and **card size proposal** (`4360e5b`) — accept, change or drop each. Live on poker-dev too; before/after screenshots in the repo under `proposals/playtest-feedback-1/`. If only part A is approved, `main` gets part A without the two proposal commits. The seat change is subtle; a bolder variant (seats lighter than the felt) was not built. Added 2026-09-28.

## inbox to triage
<!-- count only; run /triage-inbox -->
- `00_inbox/` has 1 note ([[code_docs_verification_2026_09_26]], 2026-09-26). Run `/triage-inbox`.

## resolved recently
- [x] Dev hostname — resolved 2026-09-29: `poker-dev.noctifer20.com` (free cert) instead of `dev.poker.` + ACM
- [x] Set up poker-dev by script — resolved 2026-09-29: done, live
<!-- move ticked items here with the date, keep the last ~10, then drop -->
- [x] [[playtest_feedback_2026_09_28]] — which version — resolved 2026-09-28: part of v1.0
- [x] [[playtest_feedback_2026_09_28]] F3 pace — resolved 2026-09-28: showdown, dealing, bet/raise/call too fast and unhighlighted → UI-R37
- [x] [[playtest_feedback_2026_09_28]] F2 sound — resolved 2026-09-28: sound effects + vibration, on by default; no music → UI-R38
- [x] [[playtest_feedback_2026_09_28]] F1 chips — resolved 2026-09-28: keep UI-R4 / UI-R1 for now
- [x] [[0005_hosting_dokploy_behind_cloudflare]] — resolved 2026-09-27: accepted, subdomain `poker.noctifer20.com`
- [x] `fix/heads-up-transition-bb` merge — resolved 2026-09-26: merged (`ff424a5`)
- [x] `feat/web-table` merge — resolved 2026-09-26: merged (`15c2856`)
- [x] [[design_system_ground_rules]] — nickname limit 16 — resolved 2026-09-25: confirmed
- [x] [[ui_design_system]] — landscape phones — resolved 2026-09-25: portrait-only
- [x] [[design_system_ground_rules]] — UI-R14 at 320px — resolved 2026-09-25: accepted
