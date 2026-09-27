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
- sitting out (owner, 2026-09-23): a sitting-out player is **still dealt in**, posts blinds as normal and is **auto check-or-folded** on every turn. **One timeout** sits a player out. Sitting out is bounded: after a **2-minute** grace the player is **removed from the table** automatically — the same clock as disconnects, one rule for both (owner confirmed 2026-09-25)
- top-up (owner, 2026-09-23, reading confirmed 2026-09-25): top-ups are **opt-in** (player-initiated), happen **between hands only**, bring the stack **up to 100bb** (not +100bb), and are **not available** to a stack already at or above 100bb
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
- a banned-word list for the money-vocabulary check (owner, 2026-09-23, confirmed 2026-09-25: not needed; the release check stays a manual copy review)
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
- [x] Rules spec in `04_specs/` with numbered requirements + test vectors (`poker-rules-analyst`) → [[nlhe_cash_game_rules]] ✅ 2026-09-23 — includes button rule, heads-up blinds, min-raise, side pots, odd chip, both entry mechanisms, sit-out/timeout behaviour
- [x] Scaffold `../poker-monorepo` per [[0003_monorepo_structure_and_tech_stack]] ✅ 2026-09-23 — Turborepo + pnpm workspace live, `pnpm install/build/test/lint` all pass; see log for layout and deviations
- [x] Engine: hand evaluator, betting state machine implemented against [[nlhe_cash_game_rules]]'s R1–R54 and its 9 test vectors ✅ 2026-09-24
  - [x] Hand evaluation (R4–R7), min-raise/short-all-in mechanics (R20–R26), side-pot construction + odd-chip rule (R27–R31), dead-button/heads-up assignment (R14–R19) ✅ 2026-09-23 — pure, no-IO, in `packages/engine/src/{hand-evaluator,betting-state-machine,pots,button}.ts`; 25 Vitest tests covering TV-1 through TV-8; `pnpm build/test/lint` all green. Committed `fc9d0fd` (no remote yet).
  - [x] `legalActions(state)` query; `applyAction` validates against it (one source of truth) ✅ 2026-09-24 — `af50fb2`
  - [x] Hand-lifecycle reducer `packages/engine/src/hand.ts` (`startHand` / `applyHandAction` / `voidHand` → `{state, events}`; street progression, all-in runouts, uncontested win, showdown + side-pot awards, void hands R52–R54) ✅ 2026-09-24 — `af50fb2`
- [ ] Spec revision of [[nlhe_cash_game_rules]] (`poker-rules-analyst`): replace R30's construction formula with the merged-layer rule the engine uses (R30 as written drops folded chips / double-awards odd chips); write the engine's and server's ASSUMPTIONs (see 2026-09-26 log) into numbered rules (deal order R2, short big blind R19/R20, no raise when all others all-in, sub-BB opening all-in min-raise) (R23 answered 2026-09-25: TDA — already written into R23); also add TV-R23-cumulative from the engine test (`packages/engine/test/legal-actions.test.ts`) and clarify R23's "amount that player last faced" as *the wager level the player's last action matched or set* (engine reading; the pre-action reading contradicts TV-7)
- [ ] Engine: R10 voluntary show — not yet implemented (R41a immediate post done 2026-09-26 via `StartHandInput.entryPosts`, `aa519f6`)
- [x] Engine: set `REVEAL_HOLE_CARDS_ON_UNCONTESTED_WIN = false` (R8 as amended 2026-09-25) ✅ 2026-09-25 (`bc3b00d`, branch `feat/engine-r8-r23`)
- [x] Engine: cumulative short all-ins reopen betting per TDA (R23 as amended 2026-09-25) — replace the literal-R23 ASSUMPTION, add a test vector ✅ 2026-09-25 (`bc3b00d`, branch `feat/engine-r8-r23`)
- [x] Merge `feat/engine-r8-r23` into `main` in `../poker-monorepo` ✅ 2026-09-26 (`8fce133`)
- [x] Server: top-up is an explicit player action between hands (R46, opt-in) ✅ 2026-09-26 (`feat/server-game-loop`)
- [ ] Keep the shuffle behind a single interface — cheap insurance for v3.0 (interface + CSPRNG implementation scaffolded in `packages/engine/src/shuffler.ts`; done, no further action unless v3.0 needs a new implementation)
- [x] Real-time server (Socket.IO): tables, seats, turn order, timeouts, sit-out → auto-removal, disconnect/reconnect ✅ 2026-09-26 (`feat/server-game-loop`, `e0981b6`; merged `0ceb3b0`)
- [x] Merge `feat/server-game-loop` into `main` ✅ 2026-09-26 (`0ceb3b0`)
- [ ] Fix the built server: `node dist/index.js` can't import `@poker/engine` (TS-source entry with `.js` imports) — broken on `main` too; blocks deploy ⏫
- [ ] Server: manual sit-out toggle (R38) — R38 says v1.0 has none; see [[code_docs_verification_2026_09_26]] (change entry option after joining: done on `feat/web-table`, `table:entry`)
- [ ] Server hardening before public testers: CORS (currently `*`), rate/payload limits on socket commands, JSONL rotation
- [ ] Reconcile leaving mid-hand: R42 says treat as disconnect (check-or-fold), server always folds (owner question in [[awaiting_owner_review]])
- [x] Hand-history log from the first hand (`HandHistorySink`, append-only) ✅ 2026-09-26 — every completed/voided hand written in full (R48); rotation still a TODO (hardening task above)
- [x] Hand counter: track hands completed toward the 1,000-hand exit criterion ✅ 2026-09-26 — global + per-table on `/healthz`; in-memory, resets on restart (JSONL line count is the durable number); counts hands vs auto-folding sat-out players, and voided hands separately
- [x] Table UI, mobile-first — `apps/web` table screen composed from `@poker/ui` ✅ 2026-09-26 (`feat/web-table`, merged `15c2856`)
- [x] Merge `feat/web-table` into `main` ✅ 2026-09-26 (`15c2856`)
- [ ] Web gaps: in-hand side-pot breakdown, "uncalled bet returned"/"mucked" seat states, next-hand countdown (`nextHandAt`), timer length and grace length in the protocol (client assumes 30s/120s), R10 voluntary show
- [ ] Manual checks before testers: screen reader, 320px and notched phones, 200% text, reduced motion in the running app
- [x] On-demand public table matchmaking by blinds level ✅ 2026-09-26 — fullest table with a free seat, else oldest, else a new table
- [x] Engine: fix double big blind on 3→2 transitions (R14/R16) ✅ 2026-09-26 (`fix/heads-up-transition-bb`, merged `ff424a5`)
- [x] Merge `fix/heads-up-transition-bb` into `main` ✅ 2026-09-26 (`ff424a5`)
- [ ] Check R14/R41: a busted player who tops up rejoins the rotation directly (not wait-for-BB) and may skip a big blind
- [ ] Repo: add a root Prettier config pointing at `packages/config` preset (width 100) before any repo-wide format
- [ ] Deploy somewhere people can reach (no Dockerfile yet)

## open questions
<!-- not decided — ask the owner before assuming -->
- none — all v1.0 rules questions answered by 2026-09-25.

## decisions
- [[0002_staged_delivery_free_play_first]] (accepted 2026-09-22)
- [[0003_monorepo_structure_and_tech_stack]] (accepted 2026-09-23)
- [[0005_hosting_dokploy_behind_cloudflare]] (accepted 2026-09-27)

## related
[[roadmap]] · [[vision]] · [[v1_1_private_lobbies_for_friends]] · [[glossary]] · [[nlhe_cash_game_rules]]

## log
- 2026-09-22 — created from the owner's staging brief. Variant (NLHE cash-game), platform (mobile-first web), identity (nickname only) and matchmaking (on-demand public tables by blinds level) confirmed with the owner.
- 2026-09-22 — owner answered (via `/owner-review`): 9-max tables; blinds 1/2, 5/10, 25/50; 100bb starting stack with free top-up any time; 30s timer, check-or-fold on timeout.
- 2026-09-22 — owner answered: disconnected players sit out with seat held ~2 min; no spectators in v1.0; self-hosted on a small VPS/PaaS (to be fixed in the stack ADR).
- 2026-09-23 — [[0003_monorepo_structure_and_tech_stack]] accepted; initiative set **active**. Rules spec dispatch cancelled by the owner mid-session; to be done in a separate session.
- 2026-09-23 — grilling session. Owner set the exit criterion (≥1,000 hands without an issue), sit-out rules (dealt in + auto-fold, one timeout sits you out, bounded then removed), top-up reading, both join mechanisms, muck allowed, one-player table waits indefinitely, hand histories from day one, no bot, no banned-word list, GTM out of scope, hosting region irrelevant. Stack fixed: TypeScript only, Turborepo, Socket.IO, in-memory state for v1. [[0003_monorepo_structure_and_tech_stack]] drafted.
- 2026-09-23 — `poker-rules-analyst` wrote the NLHE cash-game rules spec → [[nlhe_cash_game_rules]] (status: draft). Numbered requirements (R1–R54) + 9 test vectors covering hand-ranking ties, 2-way and 3-way side pots, heads-up order, min-raise/short-all-in edge cases, dead-button movement, and timeout/sit-out. Made the engine-level calls the initiative left open: dead button (not moving), heads-up SB-acts-first-preflop-last-postflop, TDA-style min-raise/incomplete-all-in mechanics, layered side-pot construction with odd-chip-to-left-of-button. Surfaced a new owner question (top-up opt-in vs. automatic) and a non-blocking protocol-fit note for [[v3_0_provable_randomness]] (mucked hands may never be revealable for future shuffle verification).
- 2026-09-23 — `../poker-monorepo` scaffolded per [[0003_monorepo_structure_and_tech_stack]] (run in parallel with the rules-spec work above). Turborepo + pnpm workspace initialized as its own git repo (first commit made); `apps/web` (React + Vite + Socket.IO client placeholder), `apps/server` (Express + Socket.IO, `/healthz`, `TableStore` interface + in-memory implementation, `HandHistorySink` interface + JSONL implementation), `packages/engine` (`Card`/`Deck` types, `Shuffler` interface + CSPRNG implementation, hand-evaluator/betting-state-machine left as "not implemented" stubs pending the rules spec above), `packages/protocol` (placeholder client↔server/event types), `packages/config` (shared tsconfig/eslint/prettier). `pnpm install`, `build`, `test`, and `lint` verified passing; `pnpm dev` verified live (server health check + Vite dev server both responded). Deviations from the ADR, all minor: `apps/web` hand-written rather than via `pnpm create vite` (avoids clashing with `packages/config` presets); `engine`/`protocol` consumed from TS source rather than compiled `dist`; `protocol` duplicates a small `Card`/`Suit`/`Rank` type instead of depending on `engine` (keeps it dependency-free); server uses Express instead of raw `node:http` for the thin HTTP layer; no `Dockerfile` yet. None of these need an ADR amendment — all fall within "framework picks are recommendations" in [[0003_monorepo_structure_and_tech_stack]].
- 2026-09-23 — Dispatched a fresh subagent to implement the engine's pure game logic against [[nlhe_cash_game_rules]] (scoped to `packages/engine` only — no server/timer/session work). Delivered: `hand-evaluator.ts` (best-5-of-7, R4–R7), extended `betting-state-machine.ts` (min-raise/short-all-in/reopening mechanics, R20–R26), new `pots.ts` (layered side pots + odd-chip rule, R27–R31), new `button.ts` (dead-button + heads-up assignment, R14–R19), small `card.ts` additions (`rankValue`, R3's `isValidStandardDeck`). 25 Vitest tests covering TV-1 through TV-8, including explicit chip-conservation assertions on TV-4/TV-5. Verified independently (re-ran `pnpm build/test/lint` myself, spot-read `hand-evaluator.ts` and `pots.ts`) — all green, logic checks out. **Left uncommitted** in `poker-monorepo` pending review. Judgment calls made by the agent, flagged not silently assumed: heads-up button alternates hand-to-hand between the two occupied seats (R16/R17 doesn't specify this — standard convention used); `BettingAction.amount` is a "raise to X" total, not a delta; hand-end/orchestration (single-remaining-player win, void-hand abort) deliberately left out as a higher-layer concern, not built here.
- 2026-09-24 — subagent finished the engine (`af50fb2`, `packages/engine` only): `legalActions` (to-call, min/max raise-to, bet/raise/all-in flags; `applyAction` now validates through it, `IllegalActionError` exported) and the hand-lifecycle reducer `hand.ts` (hand-history-ready event feed incl. deck + burns; invariant break → void + refund per R52). 63 engine tests (was 25) covering every TV; seeded 2,000-hand random-play run with chip conservation. Verified independently: forced uncached `turbo run build test lint` all green. Fixed a real bug in `pots.ts` — a folded blind in a split pot produced two same-eligibility layers and a 28/26 odd-chip split; adjacent equal-eligibility layers now merge (regression test added). **OPEN** R8: `REVEAL_HOLE_CARDS_ON_UNCONTESTED_WIN = true` (spec's literal wording) pending the owner. **ASSUMPTIONs** (marked in code): hole cards dealt one per pass (R2); short BB all-in for less, others still face a full BB (R19/R20); no bet/raise offered when all others are all-in; R23 read literally — cumulative short all-ins do *not* reopen (TDA says they do → owner question); adjacent same-eligibility pot layers merged (R27 over R30); bad `startHand` input throws rather than voids; folding when check is free allowed. Five wrong cross-references in the spec fixed in the vault (R3→R52, R19→R41a/R32, R24→TV-7, R30→TV-4/5). Left: R10 voluntary show, R41a immediate post; spec revision above. `packages/ui` port started in parallel on branch `feat/ui-package` (worktree `../poker-monorepo-ui`) — see [[ui_design_system]].
- 2026-09-25 — `/owner-review`: owner answered the three rules questions. **R8:** "Hidden" — an uncontested winner (everyone else folded) need not reveal; reveal is showdown-only. **R23:** "TDA: reopens" — cumulative short all-ins totalling a full raise reopen betting. **R46:** "Opt-in" — top-up is player-initiated. All three written into [[nlhe_cash_game_rules]]; engine/server tasks added above.
- 2026-09-25 — `/owner-review`, continued: owner **confirmed** the top-up reading (between hands, up to 100bb, none at ≥100bb), the **2-minute** sit-out grace (same clock as disconnects; R36 now MUST), and **no banned-word list** (manual copy review stays).
- 2026-09-25 — subagent brought the engine in line with the 2026-09-25 answers (`bc3b00d` on `feat/engine-r8-r23`, off `main`, not merged). **R8:** fold-around winner's cards hidden by default (no reveal event, `revealedSeats` empty; still logged privately for R48); showdown winners of any pot layer always reveal. **R23:** new `BettingState.closedAtBet` (wager level each seat last matched/set); a closed seat may re-raise once `currentBet − closedAtBet ≥ raiseIncrement`; shared by `legalActions`/`applyAction`. Engine tests 63 → 70 incl. TV-R23-cumulative; the seeded 2,000-hand run now asserts the reopen path is hit (4 spots). Verified myself: forced uncached `turbo run build test lint` 15/15 green (engine 70, ui 353, server 4). Noted: `closedAtBet` is a new required exported field (breaking for hand-built `BettingState`s — only tests today); Prettier not enforced and flags every engine file.
- 2026-09-26 — `feat/engine-r8-r23` merged into `main` (`8fce133`). Server game loop dispatched to a subagent on `feat/server-game-loop`.
- 2026-09-26 — subagent built the server game loop on `feat/server-game-loop` (off `8fce133`, not merged): `aa519f6` engine R41a entry posts (additive, `StartHandInput.entryPosts`), `5e43743` table manager + protocol, `e0981b6` Socket.IO transport + `/healthz` counter. Shape: pure synchronous `table.ts` over a serializable `TableState`; `view.ts` the only payload builder (allow-list per field; own hole cards + showdown reveals only); `table-manager.ts` owns matchmaking, session tokens, per-table wake-ups on an injectable `Clock`, ordered hand-history writes, counters, and voids + refunds a hand on unexpected errors; `socket-server.ts` thin adapter. Protocol in `packages/protocol` (`lobby:join`, `session:resume`, `table:action/sit-in/top-up/leave` with typed acks; `table:update {events, view}`, `table:removed`, `session:replaced`). Tests: engine 70 → 74, server 4 → 36 (fake-timer table-manager tests, 401-hand 6-table soak with chip conservation + privacy on every payload, 2 real-socket integration tests). Verified myself: forced uncached `turbo run build test lint` 15/15 green; reviewed the privacy path. **ASSUMPTIONs** (in code; to go into the spec revision): 3s between hands; default entry = wait-for-BB; lowest free seat; nicknames 1–16 chars, not unique; entry options only at an already-running table; wait-for-BB via dead-button rotation counting waiters, max one entry per hand; a hand needs ≥1 dealt-in player not sitting out (R11); leaving mid-hand always folds; reconnect only auto-sits-in a disconnect sit-out; 2-min clock from first sit-out, not restarted; disconnect on your turn auto-acts at once; top-up allowed whenever you're not dealt into the current hand; busted = sit-out on the 2-min clock, no missed-blind tracking; SB-seat entry post tops up to 1bb, BB-seat entry post ignored; voided hands counted separately. Found: built `start` script broken (also on `main`); R42 vs brief conflict on leaving (my brief said fold — owner question).
- 2026-09-26 — `feat/server-game-loop` merged into `main` (`0ceb3b0`); build/test/lint green on the merged tree (engine 74, server 36, ui 353).
- 2026-09-26 — subagent built the `apps/web` table screen on `feat/web-table` (worktree `../poker-monorepo-web`, 8 commits off `0ceb3b0`, not merged): entry (nickname, blinds from protocol `BLINDS_LEVELS`), session token + auto `session:resume`, table from `TableView` only (events animate the deal), `legalActions` → ActionBar/BetSizer with raise-to totals, timer from `actionDeadline` + clock offset from `serverTime`, sit-out countdown, busted/top-up, entry-option sheet, alone-at-table, leave confirm, offline banner, second-window takeover, portrait-only rotate prompt. Removed the "Provably random" copy; banned-word scan now covers `apps/web`. Protocol/server additions: `SeatView.removeAt` (UI-R24), `HandResultView.board`, `table:entry` command (`not-waiting` error); one `packages/ui` CSS fix (win ring on your own hole cards). Tests: server 36 → 40, ui 353 → 354, web 0 → 98 unit/integration + 4 Playwright (two browser contexts play a hand against a real server). Verified myself: forced uncached `turbo run build test lint` 15/15 green, web e2e 4/4. **ASSUMPTIONs** (in code): timer ring drawn against 30s; offline seat-hold estimated at 120s; desk layout ≥1024×600; rotate prompt = touch + landscape + ≤500px tall; always 9 seats drawn; SB/BB markers preflop only; join sends no entry option (server default wait-for-BB). Leave-sheet copy follows the server (fold + seat released), not S13 — tied to the R42 question. Root `package.json`/README still say "provably random" (not product copy).
- 2026-09-26 — owner approved; `feat/web-table` merged into `main` (`15c2856`), worktree `../poker-monorepo-web` removed. Local dev instance started for the owner's first hands-on test.
- 2026-09-26 — subagent fixed the 3→2 double big blind on `fix/heads-up-transition-bb` (worktree `../poker-monorepo-bb`, not merged): heads-up BB = next occupied seat after the previous BB (one rule for alternation, 3+→2, 2→3+, replacement); written into [[nlhe_cash_game_rules]] as R16.1. Server unchanged (`planHand` already passes the last assignment; wait-for-BB check uses the same function). Tests: engine 74 → 88 (all 3→2 variants, 2→3, replacement, seeded 2,000-run property test: between two BBs by P, every player seated throughout posts exactly one BB), server 40 → 43 (original scenario fails on old engine). Verified myself: forced build/test/lint 15/15, read the diff. ASSUMPTION → owner question (remaining heads-up player can post SB twice running). Noticed: busted top-up may skip a BB; no root Prettier config.
- 2026-09-26 — owner approved; `fix/heads-up-transition-bb` merged into `main` (`ff424a5`), worktree removed; green on the merged tree (engine 88, server 43, ui 354, web 98). R16.1 edge case queued for the owner.
- 2026-09-27 — deploy planning: owner chose one subdomain of `noctifer20.com`, Cloudflare proxy on, Dokploy on their VPS; deploys voiding live tables accepted for v1.0 (drain mode tracked in [[v1_1_private_lobbies_for_friends]]). Drafted [[0005_hosting_dokploy_behind_cloudflare]] (proposed). Added two monorepo agents in `../poker-monorepo/.claude/agents/`: `platform-engineer` (build/Docker/CI/deploy) and `security-reviewer` (read-only server/deploy review). Vault got a git remote and its first push (`2e8e269`).
- 2026-09-27 — owner accepted [[0005_hosting_dokploy_behind_cloudflare]]; subdomain confirmed as `poker.noctifer20.com`.
