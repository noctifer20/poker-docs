---
type: status
updated: 2026-09-24
tags: [status]
---

# status

> Living snapshot of where the project stands. AI-maintained; overwrite, don't append. History belongs in `01_daily/` and initiative logs.

## phase
**v1.0 — build.** v0 closed 2026-09-23 ([[define_vision_and_scope]] done, [[0003_monorepo_structure_and_tech_stack]] accepted). Staged plan agreed on 2026-09-22 ([[0002_staged_delivery_free_play_first]], accepted): v1.0 casual multiplayer → v1.1 private lobbies → v2.0 wallet & crypto (testnet) → v3.0 provable randomness. `../poker-monorepo` is now a working Turborepo skeleton (install/build/test/lint all green) with the engine's hand-evaluator and betting-state-machine still stubbed; the rules spec they'll be built against just landed.

## current focus
- [[ui_design_system]] (active, p0): design **frozen** (owner, 2026-09-24) at the Claude Design reference [Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy); rules UI-R1–R36 + 13 *known gaps* to fix in code, in [[design_system_ground_rules]] (review). Next, per [[0004_design_system_in_code]] (accepted): engine `legalActions` → port to `packages/ui`. The v1.0 table UI is built from it.
- [[v1_0_casual_multiplayer_poker]] (active): engine's pure game logic (hand evaluation, betting/min-raise, side pots, dead-button) is implemented, tested against [[nlhe_cash_game_rules]], and committed (`fc9d0fd`, no remote yet). Next: hand-lifecycle orchestration, then wire the engine into `apps/server` (Socket.IO tables/seats/timers) and `apps/web` (table UI).

## recent changes
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
- Accepted: [[0004_design_system_in_code]] (2026-09-24: repo canonical, `packages/ui`, Claude Design frozen as v1.0 reference) · [[0001_split_docs_vault_and_code_monorepo]], [[0002_staged_delivery_free_play_first]], [[0003_monorepo_structure_and_tech_stack]] (TypeScript only, Turborepo, Socket.IO, in-memory state for v1; hosting: small VPS/PaaS, provider at deploy time)
- Parked until v1.1 ships: randomness scheme, chain/custody ([[randomness_scheme_research]], [[chain_and_custody_research]]).

## blockers
- None on the engine start now: [[nlhe_cash_game_rules]] (spec) and the `../poker-monorepo` skeleton both exist. Next work is implementing hand-evaluator/betting-state-machine logic against the spec's test vectors.

## risks
- **Regulatory/legal:** real-money crypto poker is regulated in most jurisdictions. Deferred to v2.0/v3.0, not retired. **Assumption:** v1.x (free, play chips, no money vocabulary) sits outside gambling regulation — unverified.
- **Fairness claims:** nothing may be claimed until `04_specs/` has a spec (v3.0). v1.x and v2.0 must say "server shuffles" and nothing more.
- **Rework:** parking the randomness research means the v1 engine's shuffle may need redesign in v3.0. Accepted by the owner; mitigated by keeping the shuffle behind one interface.
- **Empty room:** v1.0 is public tables with no distribution (owner: GTM is a later stage). The 1,000-hand exit criterion will be reached with testers the owner brings, not strangers. Not a problem for the engine goal; noted so nobody reads v1.0 traffic as product signal.

## next actions
0. Engine `legalActions` query, then the `packages/ui` port per [[0004_design_system_in_code]], fixing every *known gap* in [[design_system_ground_rules]] with UI-R tests in CI.
1. Implement the engine (`packages/engine`: hand evaluator, betting state machine) against [[nlhe_cash_game_rules]]'s test vectors, replacing the current "not implemented" stubs.
2. Build out `apps/server` table/seat/matchmaking logic and `apps/web`'s real table UI on top of the existing scaffolds.
3. Owner: confirm the four readings/questions queued in [[awaiting_owner_review]] (top-up rule, sit-out grace, banned-word list, top-up opt-in vs. automatic); define *success looks like* in [[vision]].

## open questions
_Owner-facing ones are tracked in [[awaiting_owner_review]]; this list is the project-level summary._
- v1.0 and v1.1 parameters: answered 2026-09-22/23, recorded in [[v1_0_casual_multiplayer_poker]] and [[v1_1_private_lobbies_for_friends]]. Hosting region: deliberately not a v1 concern (owner, 2026-09-23). Two readings await confirmation (top-up rule, sit-out grace).
- Parked to v2.0/v3.0: trusted vs trust-minimised server; custodial vs non-custodial; chain and asset; jurisdictions.
