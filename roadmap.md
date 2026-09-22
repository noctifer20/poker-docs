---
type: roadmap
updated: 2026-09-23
tags: [roadmap]
---

# roadmap

Stage-by-stage plan agreed with the owner on 2026-09-22 (decision: [[0002_staged_delivery_free_play_first]]). Each milestone is a shippable version. A version is *done* when its initiative's **done when** list is fully ticked. Anything not listed under a version is **not** in that version — don't assume, ask.

## milestones

| id | milestone | outcome | initiative |
|---|---|---|---|
| v0 | design | vision, scope and this roadmap written down; monorepo structure decided ([[0003_monorepo_structure_and_tech_stack]], accepted) | [[define_vision_and_scope]] |
| v1.0 | casual multiplayer poker | strangers open the web app on a phone, pick a blinds level, get seated at a public table and play full hands of No-Limit Hold'em with play chips. Exercises the engine end to end. **Exit: ≥1,000 hands played without an issue.** No money, no accounts, no mention of payments anywhere. | [[v1_0_casual_multiplayer_poker]] |
| v1.1 | private lobbies for friends | a player creates a lobby, shares it, and the group plays together on their own phones in the same room — the app replaces a physical poker set | [[v1_1_private_lobbies_for_friends]] |
| v2.0 | wallet & crypto | accounts, a crypto wallet, deposits, stakes and payouts — on testnet / valueless tokens only | [[v2_0_wallet_and_crypto]] |
| v3.0 | provable randomness | shuffles and deals verifiable per a spec in `04_specs/`, with a verification UI; external audit. Gate for real-value play. | [[v3_0_provable_randomness]] |

### what each version is *not*
- **v1.0 / v1.1** — no payment provider, no wallet, no "buy chips", no real-money vocabulary anywhere in the product. No accounts (nickname only). No fairness claim beyond "the server shuffles".
- **v2.0** — no real value moves. Still no fairness claim; copy says plainly the shuffle is server-trusted.
- **v3.0** — real-value launch is a separate gate *after* v3.0 (audit findings closed, jurisdiction plan executed). It is not part of v3.0 itself.

## now
- [[v1_0_casual_multiplayer_poker]]

## next
- [[v1_1_private_lobbies_for_friends]]

## later
- [[v2_0_wallet_and_crypto]]
- [[v3_0_provable_randomness]]
- parked research, resumes after v1.1 ships: [[randomness_scheme_research]] · [[chain_and_custody_research]]
- real-value launch gate (after v3.0) — not an initiative yet

## done
- v0 — [[define_vision_and_scope]] (2026-09-23)
