---
type: initiative
status: done
priority: p0
milestone: v0
created: 2026-09-22
target: 
tags: [initiative]
---

# define_vision_and_scope

## goal
[[vision]] is complete enough that every later decision can be checked against it: who the game is for, the first playable shape (variant, table size, stakes model), principles, and non-goals.

## why
Everything else — randomness scheme, chain, custody, UX — depends on what we are building first. Cheapest place to be wrong is on paper.

## scope
**in:**
- pitch, target players, principles, non-goals, success criteria
- first playable game shape (variant, heads-up vs multi-way, cash vs tournament)

**out:**
- technology choices (see [[randomness_scheme_research]], [[chain_and_custody_research]])

## done when
- [ ] [[vision]] has no `_draft_` markers left — *success looks like* still draft; owner deferred it, carried in [[awaiting_owner_review]], does not block code
- [x] first playable game shape is written down
- [x] ~~target/avoided jurisdictions named~~ — dropped from v0 by the owner (2026-09-23: irrelevant for v1); lives in [[v2_0_wallet_and_crypto]] *done when* ✅ 2026-09-23
- [x] [[roadmap]] milestones reflect the scope

## tasks
- [x] Draft "who it's for" in [[vision]] ✅ 2026-09-22
- [ ] Owner defines "success looks like" in [[vision]] (carried in [[awaiting_owner_review]])
- [x] Decide the poker variant and table format for the first version (promote from [[backlog]])
- [x] Owner confirms the 2026-09-22 edits to [[vision]] (who it's for, path) ✅ 2026-09-22
- [x] Owner accepts or amends [[0002_staged_delivery_free_play_first]] ✅ 2026-09-22
- [x] Answer the v1.0 open questions in [[v1_0_casual_multiplayer_poker]] ✅ 2026-09-22
- [x] Owner accepts or amends [[0003_monorepo_structure_and_tech_stack]] — closes v0 ✅ 2026-09-23
- [x] Write initial non-goals with the owner ✅ 2026-09-22

## decisions
- [[0002_staged_delivery_free_play_first]] (accepted 2026-09-22)
- [[0003_monorepo_structure_and_tech_stack]] (accepted 2026-09-23)

## related
[[vision]] · [[roadmap]] · [[status]]

## log
- 2026-09-22 — initiative created with the workspace.
- 2026-09-22 — owner briefed the staged plan; roadmap rebuilt as v1.0 → v3.0, four version initiatives created, ADR 0002 drafted. First game shape fixed: NLHE cash-game, mobile-first web, nickname only, on-demand public tables by blinds.
- 2026-09-22 — owner accepted [[0002_staged_delivery_free_play_first]] via `/owner-review`.
- 2026-09-22 — owner confirmed [[vision]] sections *why this exists*, *who it's for*, *path*, *non-goals*; *success looks like* still draft.
- 2026-09-23 — grilling: jurisdictions dropped from v0 (owner: irrelevant for v1). v1.0 exit criterion fixed at ≥1,000 hands. Stack ADR 0003 drafted; v0 closes when it is accepted.
- 2026-09-23 — owner accepted [[0003_monorepo_structure_and_tech_stack]]. **v0 done.** Only *success looks like* in [[vision]] remains open and it is carried in the review queue. Initiative moved to `past/`.
