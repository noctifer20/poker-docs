---
type: initiative
status: proposed
priority: p3
milestone: v2.0
created: 2026-09-22
target: 
tags: [initiative]
---

# chain_and_custody_research

## goal
Understand the options for where value and game state live (which chain/L2, state channels, off-chain with on-chain settlement) and whether funds are custodial, and end with a *proposed* ADR.

## why
Chain and custody determine fees, finality/latency, the regulatory posture, and how much of the fairness scheme can be enforced by code rather than trust.

## scope
**in:**
- candidate chains/L2s and settlement patterns (fully on-chain, state channels, hybrid)
- custodial vs non-custodial implications for security, UX and regulation
- stablecoin vs native-asset stakes
- first-pass regulatory scan for real-money crypto poker (jurisdictions, licensing) — flag, not legal advice

**out:**
- randomness scheme (see [[randomness_scheme_research]])
- final legal strategy

## done when
- [ ] research notes with sources for each candidate + a regulatory overview
- [ ] comparison on cost per hand, latency, finality, maturity, tooling
- [ ] a `proposed` ADR with recommendation and "revisit when"

## tasks
- [ ] Shortlist candidate chains/settlement patterns
- [ ] Regulatory first-pass; list open questions for a lawyer
- [ ] Draft the ADR via `/new-decision`

## decisions

## related
[[randomness_scheme_research]] · [[glossary]]

## log
- 2026-09-22 — initiative proposed.
- 2026-09-22 — **parked** until v1.1 ships (owner decision, [[0002_staged_delivery_free_play_first]]). Resumes under [[v2_0_wallet_and_crypto]].
