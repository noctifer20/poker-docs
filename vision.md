---
type: vision
updated: 2026-09-22
tags: [vision]
---

# vision

> Change only with the owner's approval. Items marked _draft_ are placeholders to be confirmed or rewritten.

## pitch
A **provably random poker game powered by cryptocurrency**. Every shuffle and deal can be independently verified by any player, and stakes are held and paid out in crypto.

## why this exists
Online poker asks players to trust the operator's RNG and bankroll handling. Cryptography and blockchains make it possible to replace that trust with verification.

## who it's for
- **first (v1.x):** groups of friends who want to play poker together in the same room without a physical poker set, each on their own phone — plus anyone who wants a casual, free online game.
- **later (v2.0+):** players who want to stake crypto on poker whose fairness they can verify themselves. Segments and regions still undefined.

## principles
- **Verifiable over trusted.** If a fairness property can be checked by a player, it must be; "trust us" is the fallback of last resort and is documented as such.
- **Claims follow evidence.** We say only what a spec, test, proof or audit backs (see `CLAUDE.md`).
- **Decisions are written down.** Cryptography, chain and custody choices are ADRs.
- **Small and shippable.** Prove fairness on the simplest game shape before widening.

## non-goals (initial)
- Being a general gambling platform.
- Anonymous-by-default operation that ignores regulation.

## path
Four shippable versions, free play first and verifiable randomness last: v1.0 casual multiplayer → v1.1 private lobbies for friends → v2.0 wallet & crypto (testnet only) → v3.0 provable randomness. No real value moves before v3.0 is audited. See [[roadmap]] and [[0002_staged_delivery_free_play_first]].

## success looks like
_draft — to be defined_ (e.g. a third party can reproduce and verify any hand's shuffle from public data).

## see also
[[roadmap]] · [[status]] · [[glossary]]
