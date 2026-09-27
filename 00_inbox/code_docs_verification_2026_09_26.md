---
type: inbox
created: 2026-09-26
tags: [inbox, verification]
---

# code ↔ docs verification 2026-09-26

Two read-only subagents cross-checked the vault against `../poker-monorepo` at `main` = `0ceb3b0`. One went docs → code (every checkable claim), the other code → docs (behaviour the docs miss or contradict). Nothing was changed in either repo. Findings 1 and 2 were re-checked by the main session by reading the code.

**Overall:** the docs hold up. No false claim about a feature, commit or merge. Test counts reproduce on a forced run: engine 74, server 36, ui unit 353, ui Playwright 577; lint green. The built server `start` failure reproduces (`ERR_MODULE_NOT_FOUND` on `packages/engine/src/card.js`). Every test vector (TV-1…TV-9c, TV-R23-cumulative) has a test.

## real issues in code (fix first)
1. **Fairness claim in the web client.** `apps/web/src/App.tsx:23` renders "Provably random poker — client skeleton." Breaks UI-R2 in [[design_system_ground_rules]] and the vault's no-unearned-fairness-claims rule. The banned-word test (`packages/ui/test/copy.test.ts`) only scans `packages/ui`. Fix: change the copy; extend the scan to `apps/web/src`.
2. **Double big blind on 3 → 2 transition.** `packages/engine/src/button.ts:72-77`: when the previous button has left, heads-up falls back to button = lowest seat. Seats 1/2/3 with button 1, BB 3; seat 1 leaves → next hand button 2, BB 3 again. Seat 3 posts BB twice, seat 2 skips it. Breaks R14 in [[nlhe_cash_game_rules]]. No R16 transition tests exist. Fix: BB = next occupied seat after the previous BB; add 3→2 and 2→3 tests; write the rule into R16.

## gaps that affect upcoming work
- **No countdown data for other seats.** `SeatView` (`packages/protocol/src/snapshot.ts:32-54`) has no `removeAt`; only `YouView` does. UI-R24 needs a countdown on any sitting-out/reconnecting seat. Needed for the `apps/web` table screen.
- **Engine not browser-importable.** `packages/engine/src/index.ts` re-exports `CsprngShuffler`, which imports `node:crypto`. ADR [[0003_monorepo_structure_and_tech_stack]] says the client can import the engine. Fix: separate entry point for the shuffler.
- **Hand history drops between-hand events.** `apps/server/src/table.ts:843-844` returns early when no hand is running, so joins/leaves/disconnects/top-ups between hands are never logged. R48 says they are. Fix: log table events separately, or narrow R48.

## behaviour missing from the spec (for the spec revision)
- A busted player who tops up rejoins without owing missed blinds (`table.ts:457-459, 724-734`). Weakens R14.
- R41a entry posters get the option to check in an unraised pot (`hand.ts:76-79`).
- First hand at a table with seat gaps can have a dead SB (seats 1/3/5 → SB none, BB 3). No test.

## doc errors / contradictions
- **R38 is not a gap.** [[status]] and [[v1_0_casual_multiplayer_poker]] list "server gap: R38 manual sit-out", but R38 says v1.0 has no manual sit-out, and the code matches. Drop it or turn it into an owner question.
- Soak is **400 hands across 5 tables** (seeded), not "401-hand 6-table".
- `packages/ui` exports **23** components, not 20 (20 = component story files).
- Prettier flags **14 of 18** engine files, not every file.
- A banned-word scan **does** exist for `packages/ui` (per ADR [[0004_design_system_in_code]]); docs say there is none.
- [[design_system_ground_rules]]: line 17 still says "a future `packages/ui`"; the verification section puts screenshot tests in `apps/web` (they are in `packages/ui/e2e`).
- [[ui_design_system]]: task "move ground rules draft → review" is unticked though done.
- [[v1_0_casual_multiplayer_poker]] 2026-09-23 log says engine core "left uncommitted"; it was committed the same day as `fc9d0fd`.

## server hardening (low)
- `startHand` throwing makes the table retry every 3 s forever.
- `session:resume` performs the reconnect before rejecting a socket that already holds another seat (`socket-server.ts:121-129`).
- Internal void reasons ("server error: …") are sent to clients (`table.ts:442`, `view.ts:92`).
- R4 category ordering is only partly tested (no flush > straight, full house > flush, etc.).

## repo hygiene
- The vault has no git remote either, and everything since `ee5f6a9` (2026-09-24) is uncommitted, including daily notes for 09-25 and 09-26.
- Worktree `../poker-monorepo-ui` still exists on the merged `feat/ui-package`.
