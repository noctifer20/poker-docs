---
type: initiative
status: proposed
priority: p2
milestone: v2.0
created: 2026-09-22
target: 
tags: [initiative]
---

# v2_0_wallet_and_crypto

## goal
Players have accounts and a crypto wallet; they can deposit, stake and be paid out — on a testnet or with valueless tokens only. No real value moves in v2.0.

## why
Builds and tests the money plumbing (accounts, wallet, deposit, escrow of stakes, payout) without financial or regulatory exposure, and before the fairness scheme exists. Real value is gated behind [[v3_0_provable_randomness]] plus audit ([[0002_staged_delivery_free_play_first]]).

## scope
**in:**
- accounts: identity persists (replaces nickname-only)
- wallet integration; deposit and withdraw flows
- stakes and payouts tied to hand results
- testnet / valueless tokens only
- resume [[chain_and_custody_research]] → ADRs on chain, custody and asset, before code
- regulatory first-pass (`regulatory-analyst`) — not legal advice

**out:**
- real value / mainnet
- fairness claims or verifiable shuffle → [[v3_0_provable_randomness]]

## done when
- [ ] chain, custody and asset ADRs accepted
- [ ] a player can create an account, connect or create a wallet, deposit test tokens, play, and withdraw winnings on testnet
- [ ] product copy states plainly that the shuffle is server-trusted, not verifiable
- [ ] regulatory first-pass done; jurisdictions to target/avoid listed in [[status]] risks

## tasks
- [ ] Resume [[chain_and_custody_research]]; ADRs via `/new-decision`
- [ ] Accounts
- [ ] Wallet + deposit / withdraw on testnet
- [ ] Stakes / escrow / payout flow
- [ ] Regulatory scan via `regulatory-analyst`

## open questions
<!-- not decided — ask the owner before assuming -->
- custodial vs non-custodial
- which chain / L2; which asset (stablecoin vs native)
- do nickname-only free tables keep existing alongside accounts?

## decisions
- [[0002_staged_delivery_free_play_first]] (accepted 2026-09-22)

## related
[[roadmap]] · [[chain_and_custody_research]] · [[glossary]]

## log
- 2026-09-22 — created from the owner's staging brief. Owner chose testnet-only for v2.0; real value waits for v3.0 + audit.
