---
type: initiative
status: proposed
priority: p1
milestone: v1.1
created: 2026-09-22
target: 
tags: [initiative]
---

# v1_1_private_lobbies_for_friends

## goal
A player creates a lobby, shares it with friends (link or code), and the group plays a full home game on their own phones in the same room. The app replaces a physical poker set.

## why
First version with a real use case and real users: groups who want to play poker together without chips and cards. Also tests lobby lifecycle and the mobile table UI under real conditions.

## scope
**in:**
- create a lobby: host sets the table (owner, 2026-09-22): **custom blind amounts** (any small/big), optional **blinds-increase schedule** (tournament-like), **starting stack** (in bb), **max seats** (2–9) and **timer length**; gets a shareable link or code
- join via link/code with a nickname; lobby is private, never listed publicly
- same engine, rules and table UI as [[v1_0_casual_multiplayer_poker]] (NLHE cash-game, play chips)
- host powers mid-game (owner, 2026-09-22): **pause between hands** and **kick a player**; **no chip adjustments**
- lobby lifetime (owner, 2026-09-22): lives while non-empty, **closes after a few minutes empty**; if the host leaves, **host role passes to the next seated player**; the **host must take a seat** (no host-as-spectator)
- mobile UX polish for same-room play

**out:**
- payments, wallets, money vocabulary → [[v2_0_wallet_and_crypto]]
- accounts → [[v2_0_wallet_and_crypto]]
- fairness claims → [[v3_0_provable_randomness]]

## done when
- [ ] a host can create a private lobby and share it; friends join from the link on their phones with no sign-up
- [ ] a group of friends plays a full session in the same room using only the app
- [ ] private lobbies never appear in the public table list

## tasks
- [ ] Lobby create / share / join flow
- [x] Host controls scoped with the owner (pause between hands, kick; no chip adjustments) ✅ 2026-09-22
- [ ] Host controls implemented
- [ ] Mobile UX pass for same-room play
- [ ] Real-world test: one home game with friends, notes captured to `00_inbox/`

## open questions
<!-- not decided — ask the owner before assuming -->
- none open — all v1.1 host/lobby questions answered 2026-09-22

## decisions
- [[0002_staged_delivery_free_play_first]] (accepted 2026-09-22)

## related
[[roadmap]] · [[vision]] · [[v1_0_casual_multiplayer_poker]]

## log
- 2026-09-22 — created from the owner's staging brief ("replace a physical poker set").
- 2026-09-22 — owner answered (via `/owner-review`): host sets custom blinds (+ optional increase schedule), stack, seats, timer; mid-game host can pause and kick only; lobby lives while non-empty, host role passes on leave, host must play.
- 2026-09-23 — entry gate fixed by the owner: v1.1 starts only after v1.0 has ≥1,000 hands played without an issue. Persistent game state (Redis-like) is planned for after v1.0; same-room play is the likely trigger.
