---
type: initiative
status: active
priority: p0
milestone: v1.0
created: 2026-09-22
target: 
tags: [initiative]
---

# v1_0_casual_multiplayer_poker

## goal
A stranger can open the web app on a phone, type a nickname, pick a blinds level, get seated at a public table and play complete hands of No-Limit Texas Hold'em with play chips against other live players. The engine is exercised end to end by real play.

## why
Proves the poker engine, the real-time multiplayer plumbing and the table UI before any money or cryptography exists. Everything later (v1.1 lobbies, v2.0 wallet, v3.0 verifiable shuffle) plugs into this.

## scope
**in:**
- game: No-Limit Texas Hold'em, cash-game style — blinds, stacks, sit down / leave any time, play-chip top-ups
- table parameters (owner, 2026-09-22): **9-max** tables; three blinds levels **1/2, 5/10, 25/50**; everyone sits with **100 big blinds**; a player who busts or leaves may **top up to 100bb for free at any time**; **30-second** action timer, on timeout the player **checks if possible, otherwise folds**
- disconnects (owner, 2026-09-22): a dropped player is marked **sitting out** and auto check-or-folded on their turns; seat and chips are **held ~2 minutes**, then released
- sitting out (owner, 2026-09-23): a sitting-out player is **still dealt in**, posts blinds as normal and is **auto check-or-folded** on every turn. **One timeout** sits a player out. Sitting out is bounded: after a reasonable grace the player is **removed from the table** automatically. Proposed default: the same ~2-minute clock as disconnects (one rule for both) — confirmation queued in [[awaiting_owner_review]]
- top-up (owner, 2026-09-23, reading to confirm): top-ups happen **between hands only**, bring the stack **up to 100bb** (not +100bb), and are **not available** to a stack already at or above 100bb
- joining a table mid-hand (owner, 2026-09-23): the newcomer waits for the next hand; **both** entry mechanisms are implemented — *post a big blind now* and *wait for the big blind to reach you* — and the better one is chosen by playing. Button rule (dead vs moving) and heads-up blind order are engine decisions for the rules spec
- table lifecycle (owner, 2026-09-23): a hand needs two seated players; a table that drops to one player **waits indefinitely**
- showdown (owner, 2026-09-23): players holding a **losing hand may muck** it; the winning hand is shown
- hand histories (owner, 2026-09-23): **every completed hand is logged from day one** — the only way to notice engine bugs in real play, and the raw data v3.0 verification will need. No personal data beyond nicknames
- **no spectators** in v1.0 — only seated players see a table (revisit for [[v1_1_private_lobbies_for_friends]])
- hosting (owner, 2026-09-22): **self-hosted by the owner on a small VPS/PaaS instance**, region chosen by where the first players are; provider and region fixed in the monorepo structure & tech stack ADR
- matchmaking: public tables **on demand** — the player picks a blinds level; they join a table at that level with a free seat, otherwise a new table is created
- identity: nickname only; nothing persists between sessions
- full graphical table UI: seats, hole and community cards, chips and pot, action buttons (fold / check / call / bet / raise), turn timer
- web app, mobile-first, usable on desktop too
- engine correctness with tests: hand ranking, betting rounds, side pots, all-in, showdown
- `../poker-monorepo` structure and stack recorded as an ADR before code depends on it → [[0003_monorepo_structure_and_tech_stack]] (accepted 2026-09-23)
- stack (owner, 2026-09-23): **TypeScript only** across the repo, **Turborepo**, **Socket.IO** for real-time + reconnection, **in-memory game state** (a deploy kills all tables — accepted for v1; persistent Redis-like store planned later behind an interface)

**out:**
- anything paid or money-shaped: payments, wallets, "buy chips", real-money vocabulary — not even mentioned in the UI
- accounts, sign-up, persistent history
- user-created / private lobbies and invite links → [[v1_1_private_lobbies_for_friends]]
- fairness claims or a verifiable shuffle → [[v3_0_provable_randomness]]. v1.0 shuffles server-side and the UI claims nothing about fairness.
- other variants, tournaments
- a headless bot player (owner, 2026-09-23: not in scope)
- a banned-word list for the money-vocabulary check (owner, 2026-09-23: not needed; the release check stays a manual copy review)
- go-to-market and distribution (owner, 2026-09-23: addressed in a later stage; v1.0 does not need an audience beyond testers)

## done when
- [ ] **exit criterion (owner, 2026-09-23): at least 1,000 hands played without an issue** — proves the engine and the game state machine; nothing else gates the move to [[v1_1_private_lobbies_for_friends]]
- [ ] a player on a phone can join a public table by blinds level and play hands to showdown against other live players
- [ ] a new table is created automatically when no seat is free at the chosen blinds level
- [ ] engine test suite covers hand ranking, betting rounds, side pots, all-in and showdown edge cases (test vectors from `poker-rules-analyst`)
- [ ] the product contains no payment / money / wallet vocabulary (manual copy review before release)
- [ ] every completed hand is written to the hand-history log
- [ ] monorepo structure & stack ADR accepted; the README lets a stranger run it locally

## tasks
- [x] ADR: monorepo structure & tech stack → [[0003_monorepo_structure_and_tech_stack]] accepted ✅ 2026-09-23
- [ ] Rules spec in `04_specs/` with numbered requirements + test vectors (`poker-rules-analyst`) — includes button rule, heads-up blinds, min-raise, side pots, odd chip, both entry mechanisms, sit-out/timeout behaviour
- [ ] Engine: rules, hand evaluator, betting state machine, tests (`poker-rules-analyst` for edge cases and test vectors)
- [ ] Keep the shuffle behind a single interface — cheap insurance for v3.0
- [ ] Real-time server (Socket.IO): tables, seats, turn order, timeouts, sit-out → auto-removal, disconnect/reconnect
- [ ] Hand-history log from the first hand (`HandHistorySink`, append-only)
- [ ] Hand counter: track hands completed toward the 1,000-hand exit criterion
- [ ] Table UI, mobile-first
- [ ] On-demand public table matchmaking by blinds level
- [ ] Deploy somewhere people can reach

## open questions
<!-- not decided — ask the owner before assuming -->
- none blocking. Two readings of 2026-09-23 answers await confirmation in [[awaiting_owner_review]]: the top-up rule and the sit-out grace period.

## decisions
- [[0002_staged_delivery_free_play_first]] (accepted 2026-09-22)
- [[0003_monorepo_structure_and_tech_stack]] (accepted 2026-09-23)

## related
[[roadmap]] · [[vision]] · [[v1_1_private_lobbies_for_friends]] · [[glossary]]

## log
- 2026-09-22 — created from the owner's staging brief. Variant (NLHE cash-game), platform (mobile-first web), identity (nickname only) and matchmaking (on-demand public tables by blinds level) confirmed with the owner.
- 2026-09-22 — owner answered (via `/owner-review`): 9-max tables; blinds 1/2, 5/10, 25/50; 100bb starting stack with free top-up any time; 30s timer, check-or-fold on timeout.
- 2026-09-22 — owner answered: disconnected players sit out with seat held ~2 min; no spectators in v1.0; self-hosted on a small VPS/PaaS (to be fixed in the stack ADR).
- 2026-09-23 — [[0003_monorepo_structure_and_tech_stack]] accepted; initiative set **active**. Rules spec dispatch cancelled by the owner mid-session; to be done in a separate session.
- 2026-09-23 — grilling session. Owner set the exit criterion (≥1,000 hands without an issue), sit-out rules (dealt in + auto-fold, one timeout sits you out, bounded then removed), top-up reading, both join mechanisms, muck allowed, one-player table waits indefinitely, hand histories from day one, no bot, no banned-word list, GTM out of scope, hosting region irrelevant. Stack fixed: TypeScript only, Turborepo, Socket.IO, in-memory state for v1. [[0003_monorepo_structure_and_tech_stack]] drafted.
