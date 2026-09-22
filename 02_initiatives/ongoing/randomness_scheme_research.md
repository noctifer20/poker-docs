---
type: initiative
status: proposed
priority: p3
milestone: v3.0
created: 2026-09-22
target: 
tags: [initiative]
---

# randomness_scheme_research

## goal
Compare the credible ways to make shuffles/deals verifiable and end with a *proposed* ADR recommending one, with its trust assumptions stated plainly.

## why
The randomness scheme is the product's core promise and constrains chain choice, latency, UX and cost. It's the highest-leverage unknown.

## scope
**in:**
- candidate families: commit–reveal among players, mental-poker style protocols, verifiable shuffles with zero-knowledge proofs, VRF/oracle-based randomness, server seed + client seed hashing
- for each: who can cheat and how, collusion/abort/withholding handling, latency, on-chain cost, implementation maturity, audit history

**out:**
- picking a chain (see [[chain_and_custody_research]])
- writing the protocol spec (follow-up, lives in `04_specs/`)

## done when
- [ ] one `05_research/` note per candidate, each with sources
- [ ] comparison table on threat model, liveness (what if a player stalls?), cost, maturity
- [ ] a `proposed` ADR with a recommendation and a "revisit when"

## tasks
- [ ] Collect primary sources (papers, audited implementations) per candidate
- [ ] Write the comparison note
- [ ] Draft the ADR via `/new-decision`

## decisions

## related
[[glossary]] · [[chain_and_custody_research]]

## log
- 2026-09-22 — initiative proposed.
- 2026-09-22 — **parked** until v1.1 ships (owner decision, [[0002_staged_delivery_free_play_first]]). Resumes under [[v3_0_provable_randomness]].
