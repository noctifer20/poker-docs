---
type: glossary
tags: [glossary]
---

# glossary

Working definitions so humans and agents mean the same thing. Keep them precise; if a term gets a project-specific meaning, say so here. Definitions are from general knowledge — confirm against sources in `05_research/` before relying on them in a spec.

## fairness & randomness
- **provably fair** — a scheme where a player can verify, after the fact (or during play), that outcomes were not manipulated by the operator or other players.
- **commit–reveal** — parties publish a hash commitment to a secret first, reveal it later; the reveal is checkable against the commitment. Basis of many shared-randomness schemes.
- **mental poker** — a family of cryptographic protocols for dealing cards among mutually distrusting players with no trusted dealer.
- **VRF (verifiable random function)** — a function producing a pseudo-random output plus a proof anyone can check against a public key.
- **verifiable shuffle** — a shuffle accompanied by a proof (often zero-knowledge) that the output is a permutation of the input.
- **seed** — the input entropy from which a shuffle/deal is derived deterministically.
- **entropy source** — where unpredictability originates (players, VRF, oracle, block data). Each has different trust and manipulation properties.

## crypto & chain
- **custodial / non-custodial** — whether the operator or the player controls the funds during play.
- **escrow / settlement** — holding stakes and paying out per game outcome, on-chain or off-chain.
- **state channel** — off-chain interaction with on-chain enforcement, so most play needn't touch the chain.
- **oracle** — a service that brings external data (including randomness) on-chain.

## poker
- **hand** — one deal from shuffle to showdown/payout.
- **table** — a group of players sharing a sequence of hands.
- **heads-up** — a two-player game.
- **showdown** — revealing hole cards to determine the winner.
- **blinds** — forced bets posted before the deal (small blind, big blind); their size defines a table's stakes level.
- **cash game** — players sit down and leave whenever they like, chips keep their value hand to hand; opposed to a tournament, where everyone starts equal and plays until one remains.
- **play chips** — chips with no monetary value. All of v1.x uses only play chips.
- **sitting out** — a seated player who is not acting. *Project-specific:* in v1.x a sitting-out player is still dealt in, posts blinds and is auto check-or-folded; sitting out is bounded and ends in removal from the table.
- **muck** — to discard a hand face down without showing it. In v1.x a losing hand may be mucked; the winning hand is shown.
- **top-up** — bringing a short stack back up to the starting stack between hands. In v1.0: free, to 100bb, never above.
- **hand history** — the immutable record of one completed hand (seats, actions, cards, pots, result). Logged from v1.0's first hand.

## product
- **lobby** — a room players gather in before/while sitting at a table. *Public* lobbies are created on demand by blinds level (v1.0); *private* lobbies are created by a host and joined via link/code (v1.1).
- **on-demand table** — the v1.0 matchmaking model: a player picks a blinds level and is seated at a table with a free seat at that level, or a new table is created.
- **version (v1.0 … v3.0)** — a shippable milestone on the [[roadmap]]; its scope is exactly its initiative's *in* list.
