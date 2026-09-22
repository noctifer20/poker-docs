---
type: decision
status: accepted
date: 2026-09-22
supersedes: 
superseded_by: 
initiative: define_vision_and_scope
tags: [decision]
---

# 0002 — staged delivery: free play first, crypto second, provable randomness last

## context
The end product is a provably random poker game with crypto stakes. The scaffolded draft roadmap ordered work fairness-spec-first (old M1) before any playable app (old M2). The owner wants the reverse: a playable, free, multiplayer poker app in front of people first to test the engine; then let friends use it as a replacement for a physical poker set; and only then add money and verifiable randomness. Real-money crypto poker is regulated. Verifiable randomness is the product's core claim and has no spec yet.

## options considered
1. **Fairness spec first, then app** (original draft) — the promise is designed before code, but nothing is playable for a long time and the engine is built against an untested design.
2. **Playable app first; money and fairness later, with real value already in v2** — fastest to real stakes, but real value would ride on a server-trusted shuffle, contradicting "verifiable over trusted", and pulls regulatory exposure forward.
3. **Playable app first; money on testnet only; real value gated behind provable randomness + audit** — engine and users first, money plumbing tested without exposure, fairness before value.

## decision
Option 3. Versions, in order:
- **v1.0 casual multiplayer poker** — free, play chips, nickname only, No-Limit Texas Hold'em cash-game style, on-demand public tables chosen by blinds level, full graphical table UI, mobile-first web app. No payment provider, no wallet, no money vocabulary anywhere.
- **v1.1 private lobbies for friends** — user-created, shareable lobbies so a group can play in the same room without a physical poker set.
- **v2.0 wallet & crypto** — accounts, wallet, deposits, stakes and payouts on testnet or valueless tokens only.
- **v3.0 provable randomness** — verifiable shuffle per spec, verification UI, external audit.
- **Real-value play** is a separate gate after v3.0: audit findings closed, jurisdiction plan executed.

The randomness-scheme and chain/custody research initiatives are parked until v1.1 ships.

## consequences
- People play early; the engine and UI are tested by real use before crypto exists.
- v1.x makes no fairness claim and never mentions money. **Assumption:** this keeps v1.x outside gambling regulation — unverified until the regulatory first-pass in v2.0.
- Parking research means the v1 engine's shuffle may need rework once the v3.0 scheme is chosen. The owner accepts this.
- Each version's scope is fixed in its initiative; anything not listed is out.

## revisit when
v1.1 has shipped and research resumes (the chosen scheme may change what v2.0/v3.0 contain), or a regulatory finding changes what v2.0 may do.
