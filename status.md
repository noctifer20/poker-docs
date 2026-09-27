---
type: status
updated: 2026-09-27
tags: [status]
---

# status

> Living snapshot of where the project stands. AI-maintained; overwrite, don't append. History belongs in `01_daily/` and initiative logs.

## phase
**v1.0 — build.** v0 closed 2026-09-23 ([[define_vision_and_scope]] done, [[0003_monorepo_structure_and_tech_stack]] accepted). Staged plan agreed on 2026-09-22 ([[0002_staged_delivery_free_play_first]], accepted): v1.0 casual multiplayer → v1.1 private lobbies → v2.0 wallet & crypto (testnet) → v3.0 provable randomness. `../poker-monorepo` is now a working Turborepo skeleton (install/build/test/lint all green) with the engine complete for v1.0 play (pure rules, `legalActions`, hand-lifecycle reducer) and `packages/ui` ported from Modern Felt with all known gaps addressed; all on `main` at `ff424a5` (engine, server game loop, ui, web table screen), pushed to `origin` (`github.com:noctifer20/poker`).

## current focus
- [[ui_design_system]] (active, p0): design **frozen** (owner, 2026-09-24) at the Claude Design reference [Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy); rules UI-R1–R36 + 13 *known gaps* to fix in code, in [[design_system_ground_rules]] (review). `packages/ui` ported and merged (`2614979`): 20 components, 74 Ladle stories, 353 unit + 577 Playwright tests green; 13 known gaps fixed (SR pass still manual). Left: manual screen-reader pass, CI, portrait-only rotate prompt (owner chose portrait-only 2026-09-25).
- [[v1_0_casual_multiplayer_poker]] (active): engine done for v1.0 play — `legalActions` + hand-lifecycle reducer landed 2026-09-24 (`af50fb2`, no remote yet), every spec test vector covered. R8/R23 answers applied and merged (`8fce133`, 2026-09-26). `apps/server` game loop built and merged 2026-09-26 (`0ceb3b0`). `apps/web` table screen built 2026-09-26 and merged (`15c2856`) — first time the game is playable end to end in a browser.

## recent changes
- 2026-09-26 — 3→2 double big blind fixed (subagent, `fix/heads-up-transition-bb`, merged as `ff424a5`): heads-up BB continues from the previous BB; rule added as R16.1. Engine 74 → 88, server 40 → 43 incl. a seeded 2,000-run rotation property test; verified with forced build/test/lint 15/15. One owner question on a heads-up replacement edge case.
- 2026-09-26 — `apps/web` table screen built (subagent, `feat/web-table`, 8 commits, merged as `15c2856`): nickname → blinds → seated → full hands on `@poker/ui`, reconnect/resume, timer, sit-out/top-up/entry-option states, rotate prompt. Fixed the "Provably random" copy bug and extended the banned-word scan to `apps/web`. Protocol gained `SeatView.removeAt`, `HandResultView.board`, `table:entry`. Web 98 unit/integration + 4 Playwright (two browsers play a hand vs a real server); verified with forced build/test/lint 15/15 + e2e 4/4. Leave-sheet copy depends on the open R42 answer.
- 2026-09-26 — code ↔ docs verification (2 subagents): docs accurate, test counts reproduce. Found two code bugs — `apps/web` placeholder says "Provably random" (breaks UI-R2), and a 3→2 seat transition makes one player post BB twice (breaks R14, R16 untested) — plus spec gaps and doc drift (e.g. R38 is not a gap). All in [[code_docs_verification_2026_09_26]], untriaged.
- 2026-09-26 — server game loop built (subagent, `feat/server-game-loop`, 3 commits, merged as `0ceb3b0`): matchmaking by blinds level, seating with both entry options (engine gained R41a entry posts), 30s timer, sit-out → 2-min removal, reconnect by session token, opt-in top-up, per-player filtered views, hand history + hand counter on `/healthz`. Server tests 4 → 36 incl. a 401-hand soak; forced build/test/lint re-run and green; privacy path reviewed. The built `start` script is broken (also on `main`) — blocks deploy. New owner question on R42 (leaving mid-hand).
- 2026-09-26 — `feat/engine-r8-r23` merged into `main` (`8fce133`).
- 2026-09-25 — engine caught up with the owner's answers (`bc3b00d`, merged 2026-09-26 as `8fce133`): R8 fold-around winner keeps cards hidden; R23 cumulative short all-ins reopen betting (new `closedAtBet` state). 70 engine tests incl. TV-R23-cumulative; forced build/test/lint re-run and green.
- 2026-09-25 — `/owner-review` cleared 9 of 10 queue items: R8 (uncontested winner keeps cards hidden), R23 (TDA — cumulative short all-ins reopen), top-up opt-in + reading confirmed, 2-min sit-out grace, no banned-word list, 16-char nicknames, portrait-only phones (rotate prompt), ≈9.4px glyphs at 320px accepted. Written into [[nlhe_cash_game_rules]] and [[design_system_ground_rules]]; engine now lags the spec on R8 and R23 (tasks added). Only [[vision]] *success looks like* remains.
- 2026-09-24 — `packages/ui` ported (subagent, `feat/ui-package`) and merged into `main` with the engine work (`2614979`); verified myself on the merged tree. Found and fixed three defects in the frozen Claude Design source (pot pills overlapping at large text, 22px target, lost tabular figures). ~18 MB of darwin-only screenshot baselines now in git.
- 2026-09-24 — engine finished for v1.0 play (`af50fb2`): `legalActions` + hand-lifecycle reducer (`hand.ts`), 63 engine tests incl. a seeded 2,000-hand random-play run; verified with a forced uncached build/test/lint. Fixed an odd-chip bug in side-pot construction; the spec's R30 formula is wrong as written (revision task queued). New owner question on R23 (cumulative short all-ins). `packages/ui` port started in parallel on `feat/ui-package`.
- 2026-09-23 — design-system kickoff: [[ui_design_system]] initiative + `design-reviewer` agent created; process set by the owner (research + directions here → Claude Design prototypes/holds the system → ground-rules spec → code ADR). Research: [[poker_ui_competitors_and_table_layouts]], [[ui_visual_foundations]]. Three directions published for the owner to choose between.
- 2026-09-23 — a fresh subagent implemented the engine's pure game logic in `packages/engine` against [[nlhe_cash_game_rules]]: hand evaluation, min-raise/short-all-in mechanics, layered side pots + odd-chip rule, dead-button/heads-up assignment. 25 Vitest tests (TV-1–TV-8), `pnpm build/test/lint` all green — verified independently, not just taken on the agent's word. Committed in `../poker-monorepo` as `fc9d0fd` (no remote configured yet). Hand-lifecycle orchestration (street progression, void-hand handling) and the server/UI wiring are still open.
- 2026-09-23 — two subagents run in parallel: `poker-rules-analyst` wrote the v1.0 NLHE rules spec → [[nlhe_cash_game_rules]] (draft, R1–R54 + 9 test vectors, made the button/heads-up/min-raise/side-pot calls the initiative left open); a general-purpose agent scaffolded `../poker-monorepo` per [[0003_monorepo_structure_and_tech_stack]] (Turborepo + pnpm workspace, own git repo, first commit, `pnpm install/build/test/lint` all pass, `pnpm dev` verified live). Engine logic itself (hand evaluator, betting state machine) is still stubbed, waiting on the spec that landed alongside it. Full detail and ADR deviations in [[v1_0_casual_multiplayer_poker]]'s log.
- 2026-09-23 — owner accepted [[0003_monorepo_structure_and_tech_stack]]; v0 closed, [[define_vision_and_scope]] moved to `past/`; [[v1_0_casual_multiplayer_poker]] set active. Vault's first git commit made. Rules-spec dispatch cancelled; picked up in a separate session.
- 2026-09-23 — grilling session on the vault. Owner fixed the v1.0 exit criterion (**≥1,000 hands without an issue**), engine behaviours (sit-out dealt-in + auto-fold, one timeout sits you out, bounded then removed; top-up between hands to 100bb; both join mechanisms; muck allowed; one-player table waits), hand histories from day one, and the stack (TypeScript only, Turborepo, Socket.IO, in-memory state for v1). Bot player, banned-word list, GTM and hosting region ruled out of v1. [[0003_monorepo_structure_and_tech_stack]] drafted (proposed).
- 2026-09-22 — first `/owner-review` run: ADR 0002 accepted; vision confirmed; all v1.0 table parameters and v1.1 host/lobby rules answered and recorded in the initiatives.
- 2026-09-22 — [[awaiting_owner_review]] created as the single queue for owner decisions; wired into `CLAUDE.md` and the session skills. `/owner-review` skill added to walk the queue interactively and apply each resolution.
- 2026-09-22 — [[roadmap]] rebuilt around versions; four version initiatives created ([[v1_0_casual_multiplayer_poker]], [[v1_1_private_lobbies_for_friends]], [[v2_0_wallet_and_crypto]], [[v3_0_provable_randomness]]); ADR 0002 drafted; research initiatives parked until v1.1 ships.
- 2026-09-22 — workspace scaffolded (`poker-docs`, empty `poker-monorepo`); see [[0001_split_docs_vault_and_code_monorepo]].

## decisions
- Accepted: [[0005_hosting_dokploy_behind_cloudflare]] (2026-09-27: Dokploy on the owner's VPS, `poker.noctifer20.com` behind the Cloudflare proxy, manual deploys, one replica) · [[0004_design_system_in_code]] (2026-09-24: repo canonical, `packages/ui`, Claude Design frozen as v1.0 reference) · [[0001_split_docs_vault_and_code_monorepo]], [[0002_staged_delivery_free_play_first]], [[0003_monorepo_structure_and_tech_stack]] (TypeScript only, Turborepo, Socket.IO, in-memory state for v1; hosting: small VPS/PaaS, provider at deploy time)
- Parked until v1.1 ships: randomness scheme, chain/custody ([[randomness_scheme_research]], [[chain_and_custody_research]]).

## blockers
- None blocking. New owner question on R42 (leaving mid-hand: fold vs check-or-fold) — server currently folds; cheap to change.
- Built server can't start (`node dist/index.js` fails to import `@poker/engine`) — blocks deploy, not development.
- Vault backup: `origin` added 2026-09-27 (`github.com:noctifer20/poker-docs`) and first push made the same day. `../poker-monorepo` `main` is on `origin` and level with it; its merged feature branches are local-only.

## risks
- **Regulatory/legal:** real-money crypto poker is regulated in most jurisdictions. Deferred to v2.0/v3.0, not retired. **Assumption:** v1.x (free, play chips, no money vocabulary) sits outside gambling regulation — unverified.
- **Fairness claims:** nothing may be claimed until `04_specs/` has a spec (v3.0). v1.x and v2.0 must say "server shuffles" and nothing more.
- **Rework:** parking the randomness research means the v1 engine's shuffle may need redesign in v3.0. Accepted by the owner; mitigated by keeping the shuffle behind one interface.
- **Empty room:** v1.0 is public tables with no distribution (owner: GTM is a later stage). The 1,000-hand exit criterion will be reached with testers the owner brings, not strangers. Not a problem for the engine goal; noted so nobody reads v1.0 traffic as product signal.

## next actions
0. Owner smoke-tested the local build 2026-09-26 (no findings reported); a longer hands-on session can feed tasks.
1. Both bugs from [[code_docs_verification_2026_09_26]] are fixed on `main`; triage the rest of that note into tasks (`/triage-inbox`). Web gaps: in-hand side pots, next-hand countdown, timer/grace lengths in the protocol.
2. Deploy track per [[0005_hosting_dokploy_behind_cloudflare]], using the monorepo agents `platform-engineer` (fix the built server start → Dockerfile + README runbook → CI with Linux screenshot baselines) and `security-reviewer` (CORS, rate/payload limits, proxy/IP trust, hole-card leaks) before the first public deploy; JSONL rotation on a persistent volume.
3. Spec revision of [[nlhe_cash_game_rules]] (`poker-rules-analyst`): fix R30, write engine + server ASSUMPTIONs into rules, add TV-R23-cumulative, clarify R23 wording, apply the R42 answer. Engine gap: R10 voluntary show. Server gap: R38 manual sit-out.
4. Testers toward the 1,000-hand exit; manual VoiceOver/TalkBack pass before testers.
5. Owner: answer R42; define *success looks like* in [[vision]] (deferred twice).

## open questions
_Owner-facing ones are tracked in [[awaiting_owner_review]]; this list is the project-level summary._
- v1.0 and v1.1 parameters: answered 2026-09-22/23, recorded in [[v1_0_casual_multiplayer_poker]] and [[v1_1_private_lobbies_for_friends]]. Hosting region: deliberately not a v1 concern (owner, 2026-09-23). Top-up reading and sit-out grace (2 min) confirmed 2026-09-25.
- Parked to v2.0/v3.0: trusted vs trust-minimised server; custodial vs non-custodial; chain and asset; jurisdictions.
