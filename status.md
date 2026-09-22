---
type: status
updated: 2026-09-23
tags: [status]
---

# status

> Living snapshot of where the project stands. AI-maintained; overwrite, don't append. History belongs in `01_daily/` and initiative logs.

## phase
**v1.0 — build.** v0 closed 2026-09-23 ([[define_vision_and_scope]] done, [[0003_monorepo_structure_and_tech_stack]] accepted). Staged plan agreed on 2026-09-22 ([[0002_staged_delivery_free_play_first]], accepted): v1.0 casual multiplayer → v1.1 private lobbies → v2.0 wallet & crypto (testnet) → v3.0 provable randomness. Nothing built yet; the monorepo is empty. Rules spec in progress; code starts once it lands.

## current focus
- [[v1_0_casual_multiplayer_poker]] (active): rules spec in `04_specs/` (`poker-rules-analyst`), then scaffold `../poker-monorepo` per [[0003_monorepo_structure_and_tech_stack]] and build the engine against the spec's test vectors.

## recent changes
- 2026-09-23 — owner accepted [[0003_monorepo_structure_and_tech_stack]]; v0 closed, [[define_vision_and_scope]] moved to `past/`; [[v1_0_casual_multiplayer_poker]] set active. Vault's first git commit made. Rules spec dispatched.
- 2026-09-23 — grilling session on the vault. Owner fixed the v1.0 exit criterion (**≥1,000 hands without an issue**), engine behaviours (sit-out dealt-in + auto-fold, one timeout sits you out, bounded then removed; top-up between hands to 100bb; both join mechanisms; muck allowed; one-player table waits), hand histories from day one, and the stack (TypeScript only, Turborepo, Socket.IO, in-memory state for v1). Bot player, banned-word list, GTM and hosting region ruled out of v1. [[0003_monorepo_structure_and_tech_stack]] drafted (proposed).
- 2026-09-22 — first `/owner-review` run: ADR 0002 accepted; vision confirmed; all v1.0 table parameters and v1.1 host/lobby rules answered and recorded in the initiatives.
- 2026-09-22 — [[awaiting_owner_review]] created as the single queue for owner decisions; wired into `CLAUDE.md` and the session skills. `/owner-review` skill added to walk the queue interactively and apply each resolution.
- 2026-09-22 — [[roadmap]] rebuilt around versions; four version initiatives created ([[v1_0_casual_multiplayer_poker]], [[v1_1_private_lobbies_for_friends]], [[v2_0_wallet_and_crypto]], [[v3_0_provable_randomness]]); ADR 0002 drafted; research initiatives parked until v1.1 ships.
- 2026-09-22 — workspace scaffolded (`poker-docs`, empty `poker-monorepo`); see [[0001_split_docs_vault_and_code_monorepo]].

## decisions
- Accepted: [[0001_split_docs_vault_and_code_monorepo]], [[0002_staged_delivery_free_play_first]], [[0003_monorepo_structure_and_tech_stack]] (TypeScript only, Turborepo, Socket.IO, in-memory state for v1; hosting: small VPS/PaaS, provider at deploy time)
- Parked until v1.1 ships: randomness scheme, chain/custody ([[randomness_scheme_research]], [[chain_and_custody_research]]).

## blockers
- Engine code waits on the rules spec in `04_specs/` (in progress via `poker-rules-analyst`). Scaffolding the monorepo can proceed in parallel.

## risks
- **Regulatory/legal:** real-money crypto poker is regulated in most jurisdictions. Deferred to v2.0/v3.0, not retired. **Assumption:** v1.x (free, play chips, no money vocabulary) sits outside gambling regulation — unverified.
- **Fairness claims:** nothing may be claimed until `04_specs/` has a spec (v3.0). v1.x and v2.0 must say "server shuffles" and nothing more.
- **Rework:** parking the randomness research means the v1 engine's shuffle may need redesign in v3.0. Accepted by the owner; mitigated by keeping the shuffle behind one interface.
- **Empty room:** v1.0 is public tables with no distribution (owner: GTM is a later stage). The 1,000-hand exit criterion will be reached with testers the owner brings, not strangers. Not a problem for the engine goal; noted so nobody reads v1.0 traffic as product signal.

## next actions
1. Land the rules spec in `04_specs/` (numbered requirements + test vectors); owner reviews its open rule choices.
2. Scaffold `../poker-monorepo` per [[0003_monorepo_structure_and_tech_stack]]; build the engine against the spec's test vectors.
3. Owner: confirm the three readings queued in [[awaiting_owner_review]]; define *success looks like* in [[vision]].

## open questions
_Owner-facing ones are tracked in [[awaiting_owner_review]]; this list is the project-level summary._
- v1.0 and v1.1 parameters: answered 2026-09-22/23, recorded in [[v1_0_casual_multiplayer_poker]] and [[v1_1_private_lobbies_for_friends]]. Hosting region: deliberately not a v1 concern (owner, 2026-09-23). Two readings await confirmation (top-up rule, sit-out grace).
- Parked to v2.0/v3.0: trusted vs trust-minimised server; custodial vs non-custodial; chain and asset; jurisdictions.
