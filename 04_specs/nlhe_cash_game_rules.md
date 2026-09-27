---
type: spec
status: draft
area: poker-rules
created: 2026-09-23
updated: 2026-09-25
tags: [spec, poker-rules]
---

# nlhe_cash_game_rules

<!-- status: draft | review | stable | deprecated -->

## purpose
Defines the complete rules of play for the No-Limit Texas Hold'em cash-game engine built for [[v1_0_casual_multiplayer_poker]]: hand structure, blinds and button movement, betting and min-raise rules, all-in and side-pot construction, showdown and hand ranking, table/seat lifecycle (joining, leaving, disconnects, sit-out, timers), top-ups, and hand-history logging.

This is what `../poker-monorepo`'s engine is built and tested against, and what the initiative's "Rules spec" and "Engine: rules, hand evaluator, betting state machine, tests" tasks point to. It implements the product decisions in [[v1_0_casual_multiplayer_poker]] (read that note for the *why*) and adds the engine-level rulings the initiative explicitly left to this spec: dead vs. moving button, heads-up order, min-raise mechanics, side-pot/odd-chip construction, and all-in edge cases.

**Ruleset baseline.** Two published rulebooks are cited throughout:
- **Robert's Rules of Poker (RROP), v11** — a cardroom/cash-game rulebook, the closest published match to "cash game, no tournament structure." Mirror: [pagat.com/docs/RobsPkrRules11.pdf](https://www.pagat.com/docs/RobsPkrRules11.pdf) (retrieved 2026-09-23). RROP is treated as **primary** for anything cash-game-specific (dead button, odd chip).
- **Poker Tournament Directors Association (TDA) Rules** — the most precise published text for betting mechanics (min-raise, incomplete all-ins), which are identical in cash games and tournaments in every major cardroom despite the TDA's tournament framing. Source page: [pokertda.com/view-poker-tda-rules](https://www.pokertda.com/view-poker-tda-rules/); full text PDF: [pokertda.com Poker-TDA-Rules-2015](https://www.pokertda.com/wp-content/uploads/2011/01/Poker-TDA-Rules-2015-Version-1.0-full-longform-PDF-1.pdf) (retrieved 2026-09-23).

**Verification caveat on citations:** both rulebooks were retrieved as PDFs; the exact rule numbers and quoted wording below came through secondary text extraction (search-result summaries and an LLM-assisted fetch), not a byte-for-byte read of the primary PDF text layer. The **substance** (dead button behaviour, incomplete-all-in mechanics, heads-up order, odd-chip-to-left-of-button) is corroborated by multiple independent secondary sources (PokerNews, TDA's own site, a private-games mirror of RROP) and matches this analyst's general poker-rules knowledge, so it is used with confidence. The **specific rule numbers cited** (e.g. "TDA Rule 43") are **unverified** — treat them as pointers to go re-check in the primary PDF, not as citable rule numbers themselves.

Anything below with no citation and no "engine decision" tag is universal Hold'em convention (e.g. hand-ranking order) — low-risk, from general knowledge, not separately verified against a primary source.

## requirements

Numbered R1… so engine code, tests and this note's own test vectors can cite them. MUST = required for correctness; SHOULD = recommended, deviation should be a deliberate, logged choice; MAY = optional.

### game & deck
- **R1** (MUST) One standard 52-card deck, no jokers, shuffled fresh each hand.
- **R2** (MUST) The engine deals from a single ordered array representing the whole hand's card consumption in this fixed sequence: 2 hole cards to each player in seat order starting left of the button → burn → 3 flop cards → burn → 1 turn card → burn → 1 river card. Burn cards are consumed but never shown.
  - **Engine decision, forward-looking:** burn cards have no cheating-prevention value against a software RNG in v1.0 (no marked cards, no bottom dealing). They are kept anyway so the *shape* of a dealt hand — a single committed card sequence with burns at fixed offsets — is exactly what a future commit-reveal/mental-poker scheme ([[v3_0_provable_randomness]]) would need to commit to and later reveal/verify. Cheap to keep now, expensive to retrofit later. No fairness property is claimed by keeping this shape — see [[roadmap]]'s "what each version is not."
- **R3** (MUST) The engine MUST validate that a dealt deck contains 52 unique cards before dealing. A duplicate-card or short-deck condition is an invariant violation — see R52 (void hand).

### hand ranking & showdown
- **R4** (MUST) Standard high-hand ranking, high to low: straight flush > four of a kind > full house > flush > straight > three of a kind > two pair > one pair > high card. A player's hand is the best 5-card combination from their 2 hole cards + the 5 board cards (any mix, including 0 or both hole cards — "plays the board" is legal).
- **R5** (MUST) Ace plays high or low only at the ends of a straight/straight-flush (A-2-3-4-5, the "wheel," is the lowest straight). K-A-2-3-4 is **not** a straight.
- **R6** (MUST) Ties within a hand category are broken by comparing ranks in descending order (category rank, then kickers). If two or more players' best 5-card hands are rank-for-rank identical, the pot (or pot layer, see side pots) is split equally among them. See TV-1, TV-2.
- **R7** (MUST) Suits never break ties in hand value. No suit ranking exists in this variant. See TV-1.
- **R8** (MUST) A player who is the sole or a tied **winner of any pot layer** must reveal their hole cards to claim it — a winning hand cannot be mucked. A player who does not win any pot layer they were eligible for **may** muck without revealing. This applies **only at showdown**: a player who wins because every other player folded (no showdown) is **not** required to reveal and may keep their hole cards hidden. (Owner decision, [[v1_0_casual_multiplayer_poker]]; uncontested-win carve-out confirmed by the owner 2026-09-25.)
- **R9** (SHOULD) Reveal order at showdown (who is shown/asked to reveal first) follows the traditional convention — the last aggressor on the river shows first, else the first active player left of the button shows first, then clockwise — **for the UI's reveal animation only**. This is a SHOULD, not a MUST: the server already holds every hole card and computes every pot's winner(s) directly, so unlike a live table the reveal order has no effect on the actual outcome. **Engine decision.**
- **R10** (MUST) Default client behaviour: winning hand(s) are shown automatically; non-winning hands are mucked automatically. **Engine decision:** a player MAY optionally show a losing hand voluntarily (a "show" action) after the hand is decided; this is a convenience, not a rules requirement, and may be deferred past v1.0 if not needed.

### table, seats & button
- **R11** (MUST) A hand may only be dealt when **≥2 seats are occupied** and ready. (Owner decision.)
- **R12** (MUST) If a table's occupied-seat count drops to 1, the table **pauses** — no new hand is dealt — and waits indefinitely for a second player. The table MUST NOT be closed while ≥1 player remains seated. (Owner decision.)
- **R13** (engine decision, assumption) If a table's occupied-seat count drops to 0, the table MAY be reclaimed/destroyed by matchmaking. Not specified by the owner; flagged as an assumption since on-demand tables are otherwise described as ephemeral.
- **R14** (MUST) **Dead button, not moving button.** The button's seat is tied to the fixed rotation of blind duty, not forced onto the next occupied seat when the geometrically-next seat has emptied. **Engine decision — recommended over moving button** because it is the only one of the two that guarantees every continuously-seated player posts exactly one small blind and one big blind per orbit regardless of who joins or leaves between hands — which will happen constantly in an on-demand public-table product where nobody has to leave "at a good time." A moving button lets a leaving-and-rejoining player skip blinds; a dead button never does. Cites: RROP's dead-button convention (cash-game standard) and TDA's dead-button rule (tournament-framed but mechanically identical) — see purpose section for the verification caveat on exact rule numbers.
- **R15** (MUST) Concrete algorithm (this analyst's implementable formalization of R14, since the published rulebooks describe the *outcome*, not pseudocode):
  1. Seats are numbered in a fixed clockwise order.
  2. The new hand's **big-blind seat** = the next occupied seat, in numeric order, after last hand's big-blind seat.
  3. The new hand's **small-blind seat** = the seat immediately preceding the new big-blind seat in numeric order. If that seat is currently empty, the small blind is **dead** (not posted) for that hand only.
  4. The **button** = the seat (or empty slot) immediately preceding the small-blind seat/slot in numeric order.
  5. Exception: once exactly 2 seats are occupied, apply the heads-up rule (R17) instead — see R16.
- **R16** (MUST) A table transitioning to exactly 2 occupied players applies heads-up posting (R17) starting with the next hand; a table transitioning from 2 to ≥3 resumes R15 starting with the next hand, continuing the existing button rotation.
  - **R16.1** (MUST, engine decision 2026-09-26) Heads-up blind assignment after the table's first hand: the big blind is the next occupied seat after the previous hand's big-blind seat (as R15 step 2); the other player is button and posts the small blind (R17). So two players who stay alternate; on a 3+→2 transition the player who just posted the big blind never posts it again next hand and nobody is skipped; 2→3+ continues R15 from the previous big blind. **Open (owner):** if the previous big blind leaves a heads-up table and the newcomer sits clockwise between the vacated seat and the remaining player, the newcomer takes the big blind and the remaining player posts the small blind two hands running (current engine ASSUMPTION). Verification: `packages/engine/test/button-transitions.test.ts` (incl. a seeded 2,000-run property test), server table-manager tests.
- **R17** (MUST) **Heads-up (exactly 2 active players):** the button posts the small blind. The button/small-blind acts first preflop and **last** on every subsequent betting round (flop, turn, river); the big blind acts first postflop. Cite: TDA's heads-up rule (see purpose section caveat on exact rule number) and universal heads-up convention.
- **R18** (engine decision) First hand at a newly created table: button = the lowest-numbered occupied seat. A physical high-card draw (the traditional way to fairly assign the first button at a live table) is redundant here — the server already assigns seats, so any deterministic tie-break is equally fair.
- **R19** (MUST) Forced bets (small blind, big blind, and the immediate-post option in R41a) are posted automatically by the engine; they are never subject to the action timer (R32) — a player cannot "time out" a mandatory forced post.

### betting & min-raise
- **R20** (MUST) Minimum opening bet in any betting round = 1 big blind (the table's current blind level).
- **R21** (MUST) A raise must bring the total wager-to-call up by **at least the size of the largest full bet or raise made so far in that betting round** (the "raise increment"). Example: bet is 10, a player raises to 30 (increment 20) — the next full raise must be to at least 50 (another increment of ≥20).
- **R22** (MUST) A player may always go all-in for their entire remaining stack, even if it is **less than** a full raise (a "short" or "incomplete" all-in).
- **R23** (MUST) A short all-in does **not** reopen the betting round for players who already acted and already matched the previous full bet/raise in that round — they may only call the new (higher) total or fold; they may **not** re-raise, unless a later player makes a full raise (R25), which reopens the round fully. **Cumulative short all-ins (TDA):** if two or more short all-ins made after a player's last action together raise the wager-to-call by at least a full raise increment (R21) over the amount that player last faced, betting **is** reopened for that player. (Owner decision 2026-09-25: follow TDA.)
- **R24** (MUST) A short all-in does **not** change the "raise increment" used to compute the minimum for the *next* full raise — that stays anchored to the last full (non-short) bet/raise, not to the short all-in's smaller increment. See TV-7.
- **R25** (MUST) A full raise (≥ the required increment, R21) always fully reopens the betting round for every player still active in the hand, including those who already acted.
- Cites: TDA's incomplete-all-in and min-raise rules — see purpose section caveat on exact rule numbers; principle independently corroborated by multiple secondary sources.
- **R26** (design note, digital-only) Because this is a digital client with enforced bet-sizing UI, physical-table edge cases that exist only because chips are physically splashed or bets are verbally declared (string raises, verbal-declaration disputes, angle-shooting via chip splashing) do not apply and are out of scope for this spec.

### all-in & side pots
- **R27** (MUST) When one or more players are all-in for less than the current total wager, the pot is split into a **main pot** plus one **side pot per distinct all-in contribution level**, in ascending order of contribution.
- **R28** (MUST) A player is eligible to win a given pot layer only if their total contribution reaches that layer's threshold **and** they have not folded.
- **R29** (MUST) The uncalled portion of a bet/raise — when no remaining player can or will call it in full — is returned to the player who made it **before** pots are constructed; it never enters any pot.
- **R30** (MUST) Construction algorithm: let the distinct contribution thresholds among still-live money be t1 < t2 < … < tk (the highest tk being the largest contribution among players who are still live for showdown, i.e. not folded and not owed an uncalled-bet refund). For each threshold ti: `pot_i = (t_i − t_{i−1}) × (count of players who contributed ≥ t_i)`; eligible players for pot_i = those who contributed ≥ t_i and have not folded. See TV-4 and TV-5 for worked examples.
- **R31** (MUST) **Odd chip rule.** When a pot layer must be split between ≥2 tied winners and does not divide evenly into the smallest chip unit in play, the extra chip(s) are awarded **one at a time, in seat order clockwise starting from the first seat left of the button, cycling through the tied winners in that order until the remainder is exhausted.** Cite: WSOP/TDA odd-chip convention, "odd chip goes to the first seat left of the button" — [PokerNews, "Five Poker Rules You Didn't Know Existed"](https://www.pokernews.com/strategy/five-poker-rules-you-didnt-know-existed-26946.htm) (retrieved 2026-09-23); multi-chip cycling is this analyst's extension of the single-odd-chip rule to the ≥2-extra-chip case (not separately found in a primary source — flagged as **engine decision**, low-risk since it's a strict generalization of the same clockwise-from-button principle).

### timers, disconnects & sit-out
- **R32** (MUST) 30-second action timer per decision. On timeout: check if legal, otherwise fold. (Owner decision.)
- **R33** (MUST) A timeout marks the player **sitting out**, regardless of whether the default action taken was a check or a fold. (Owner decision: "one timeout sits a player out.")
- **R34** (MUST) A disconnected player is immediately marked sitting out. (Owner decision.)
- **R35** (MUST) A sitting-out player (whether from disconnect or timeout) is still dealt in, still posts blinds when due, and is auto check-or-folded on every subsequent turn until they act again (reconnect/return) or are removed (R36).
- **R36** (MUST) A sitting-out player's seat and chips are held for a grace period of **2 minutes**, the same as the disconnect hold — one clock for both cases (owner decision 2026-09-25). Implementers should key this off a single named constant (e.g. `SIT_OUT_GRACE_SECONDS`).
- **R37** (engine decision) When a held seat's grace period elapses, the seat is released and its remaining stack is discarded (not transferable, not credited anywhere) — consistent with "nickname only, nothing persists between sessions." There is no cash-out path in v1.0.
- **R38** (engine decision, assumption) v1.0 has no manual "sit out next hand" toggle — sitting out is only ever a *consequence* of a disconnect or a missed timer, never a voluntary player action. Flagged as an assumption; a manual toggle is a plausible v1.1+ addition, not built now.
- **R39** (MUST) No time bank exists in v1.0 — every decision gets exactly the flat 30-second timer, with no accrual or borrowing from future turns. (Follows from the owner's scope, which specifies only a flat timer.)

### joining & leaving
- **R40** (MUST) A player joining an in-progress hand waits for the next hand to be dealt in; they never join mid-hand. (Owner decision.)
- **R41** (MUST) **Both** entry mechanisms are available to a joining player, per the owner's instruction to decide the better one by playtesting:
  - **(a) Post and play immediately:** the newcomer posts an amount equal to 1 big blind as a live bet into the very next hand's pot and is dealt in immediately, acting in their normal turn order based on physical seat position. **Engine decision on mechanics:** this post does *not* grant the newcomer the big blind's positional privileges (e.g. last preflop action) and does *not* disturb the ordinary dead-button rotation (R15) for the other continuously-seated players — it is a straight buy-in-at-BB-cost, not a seat swap. A consequence (accepted, matches the well-known real-world trade-off of this option at live tables) is that the newcomer's *next* natural big blind, whenever the button rotation reaches their seat, still applies normally — they can pay two big-blind-equivalents in quick succession. This is intentional, not a bug.
  - **(b) Wait for the big blind:** the newcomer is seated but dealt out (no hole cards, no posting, no acting) until the button rotation's big-blind assignment reaches their seat for the first time, at which point they post the big blind and join the rotation normally from then on.
  - Both are implemented; which is offered/preferred by default is a product decision to be made by playtesting per the initiative, not fixed by this spec.
- **R42** (MUST) A player may leave at any time between hands (cash-game convention: sit down/leave freely, per [[v1_0_casual_multiplayer_poker]] scope). Leaving mid-hand is treated identically to a disconnect (R34–R37).

### top-up
- **R43** (MUST) Top-ups happen **between hands only**, never mid-hand.
- **R44** (MUST) A top-up brings a stack up to exactly 100 big blinds of the table's current blind level — never above, and never a flat "+100bb" addition.
- **R45** (MUST) Top-up is **not offered** to a stack already at or above 100bb.
- **R46** (MUST) Top-up is **opt-in / player-initiated**: the client offers a "top up" action to an eligible player between hands; the engine never silently tops up a stack without that action. Rationale: preserves player agency over stack size (a player may deliberately prefer to keep less at risk); matches the "may top up" wording in [[v1_0_casual_multiplayer_poker]] and the standard cash-game convention that reloads are player-requested, not automatic. (Owner decision 2026-09-25.)

### hand history
- **R47** (MUST) Every hand that reaches the point of dealing hole cards to ≥2 players is logged as a completed hand-history record, from the very first hand played, append-only. (Owner decision; ties to `HandHistorySink` in [[0003_monorepo_structure_and_tech_stack]].)
- **R48** (MUST) A hand-history record contains at minimum: table id, hand sequence number, timestamp, blind level, seat layout (nicknames only), button/SB/BB seats, **the complete dealt card sequence including burns and any mucked/never-shown hole cards**, every action taken (fold/check/call/bet/raise with amounts), every timeout/sit-out/disconnect/reconnect event, the pots constructed and their winners/amounts. **Rationale for logging cards nobody saw:** the initiative explicitly states hand histories are "the raw data v3.0 verification will need" — a verifiable-shuffle scheme cannot be retrofitted onto a log that only recorded what was shown at the table.
- **R49** (MUST) No personal data beyond the player's chosen nickname is recorded (no IP, device id, session token, etc.). (Owner decision.)
- **R50** (MUST) The hand-history log is append-only — no in-place edits to a completed record — so it can serve as an audit trail for chip-count disputes and future verification work.
- **R51** (MUST) A hand that is **voided** (R-Void below) is still logged, marked as void with the reason, rather than silently dropped.

### invalid state / voided hands
- **R52** (MUST) If the engine detects an invariant violation mid-hand — duplicate or missing cards (R3), a pot total that doesn't equal the sum of contributions, a stack going negative, or equivalent — it MUST abort the hand: return every player's chips to their pre-hand stack, and log the hand as **void** with the violated invariant as the reason (R51). No payout is computed from a void hand.
- **R53** (MUST) A hand left incomplete because of a server restart (in-memory state, per [[0003_monorepo_structure_and_tech_stack]] — "a deploy kills all tables") is void by construction: since state isn't persisted, there is nothing to resume, and no partial-hand chip movement survives the restart to need reconciling.
- **R54** (SHOULD) A stalled or absent client acknowledgement at showdown (e.g. a client that never sends a manual "show/muck" choice) MUST NOT block pot payout — the server computes winners and applies payouts immediately once wagering is complete, independent of any client UI state. The optional voluntary-show window (R10) SHOULD have its own short bounded timeout (e.g. 10s), after which mucking is finalized by default.

## design

Per-hand lifecycle: `await ≥2 seated → assign button/blinds (R14–R18) → post blinds (R19) → deal hole cards (R2) → betting round preflop → burn+flop → betting round → burn+turn → betting round → burn+river → betting round → showdown (R8–R10) or single-remaining-player win (no showdown, no forced reveal) → construct pots (R27–R31) → payout → append hand history (R47–R51) → offer top-ups (R43–R46) to eligible seats → next hand`.

A betting round ends when every player still live has acted and either matched the current wager-to-call or is all-in; short all-ins are tracked separately from full raises for reopening purposes (R23–R25).

## threat model / failure modes

This section is about **game-state correctness**, not cryptographic fairness — v1.0 makes no fairness claim (see [[vision]] / [[roadmap]]); that gate is [[v3_0_provable_randomness]]. These are the edge cases that break naive implementations:

| failure mode | what breaks naively | this spec's answer |
|---|---|---|
| Disconnect mid-hand | Hand hangs forever waiting on an action that never comes | R34–R37: auto sit-out, auto check-or-fold, bounded 2-minute grace hold |
| Timeout vs. no time bank | Implementers often assume a time bank exists "because most rooms have one" | R39: explicitly none in v1.0 |
| Simultaneous / cascading all-ins | Side-pot math done as a single split instead of layered pots; short all-ins mistakenly treated as reopening action | R27–R31 layered construction; R22–R25 short-all-in handling; TV-3, TV-4, TV-5 |
| Short all-in "resets" the raise increment | A naive implementation lets a 15-chip short all-in set the new minimum raise to +15 instead of keeping the prior full increment | R24; TV-4 |
| Misdeal / invalid state | Undefined behaviour, or worse, silently wrong payouts | R52: hard-void with refund and logged reason |
| Table breaking to 1 player | Table either force-closes (violates owner rule) or spins forever trying to deal | R12: pause, wait indefinitely, no forced hand |
| Chip-count disputes | No authoritative record to resolve a "you shorted me" report | R48/R50: complete append-only hand history is the resolution mechanism |
| Stalled reveal | Payout blocked on a client that never confirms show/muck | R54: server-side payout is independent of client acknowledgement |
| Joining mid-orbit exploited to skip blinds | Moving button lets a player leave right before their blind and rejoin right after | R14/R15: dead button closes this gap structurally |

## protocol fit for future verifiable randomness ([[v3_0_provable_randomness]])

v1.0's shuffle is server-trusted and no fairness property is claimed (per [[vision]]/[[roadmap]]). This section flags where today's rules would or wouldn't support a future commit-reveal/mental-poker scheme, so v3.0 planning starts from a known set of constraints rather than rediscovering them.

- **Hole card dealing** — supported today because the engine already deals from a single ordered deck array (R2). A commit-reveal scheme would commit to that array's order (hash it, or secret-share it) before dealing; nothing in this spec's ordering assumption conflicts with that.
- **Burn cards** — kept in the fixed sequence today specifically so a future commitment covers them too (R2's rationale). No change needed later.
- **Showdown reveal** — today the server decrypts/holds everything and just tells clients the answer (R9–R10). A cryptographic scheme instead needs each player to disclose a key/share to reveal their own hole cards. That composes fine for hands that go to a real showdown.
- **Muck vs. verifiability — an open tension, not resolved here.** R8 lets a losing hand be mucked without ever being shown to opponents. If "shown to opponents" and "revealed to the verification protocol" are the same reveal step in a future mental-poker scheme, a hand that's mucked at every table (nobody ever contests a side pot with a weak hand, folds happen instead) may never get its hole cards revealed at all — which means that hand's shuffle can never be verified from public data, only the hands that went to a shown showdown. Two ways to close this gap exist (mandatory reveal-to-a-private-verification-channel even when mucked at the table; or accepting a weaker guarantee that only shown hands are verifiable) and neither is chosen here. **This is flagged for [[v3_0_provable_randomness]] planning, not a v1.0 blocker** — v1.0's muck rule (R8) does not need to change for this spec to be internally consistent, since v1.0 makes no verifiability claim yet.
- **Stalls/aborts mid-hand** — today's answer is R52/R53: full refund to pre-hand stacks, hand voided and logged, nothing settled. This is straightforward because play chips carry no external value. It will need re-deriving once real value is at stake ([[v2_0_wallet_and_crypto]]) — "who is refunded, who forfeits" stops being free to answer once chips are money, and that question is explicitly out of this spec's scope.

## verification

v1.0 verification is **correctness testing**, not a fairness proof: an engine test suite (initiative task "Engine: rules, hand evaluator, betting state machine, tests") asserting against this spec's requirement numbers and the test vectors below. Each test vector cites the R-numbers it exercises so a failing test points back to the exact requirement in question. No claim beyond "the engine implements these rules correctly" is made or should be inferred from passing tests — see [[vision]]'s "no unearned fairness claims" principle.

## test vectors

Card notation: rank + suit letter (`h`♥ `d`♦ `c`♣ `s`♠), `T`=10. Board dealt left to right = flop(3), turn, river.

### TV-1 — hand-ranking tie, suits must not matter (R6, R7)
- Board: `2c 5d 9h Jc Kc`
- Player A: `Ah Qd` → best five: A-K-Q-J-9 (high card)
- Player B: `As Qc` → best five: A-K-Q-J-9 (high card)
- **Expected:** exact tie (rank-for-rank identical); pot split 50/50 despite A holding a different suit combination than B. A naive suit-aware comparator would wrongly break this tie — it must not.

### TV-2 — board plays (R4, R6)
- Board: `Th Jc Qd Kh As` (Broadway straight on the board; only 2 hearts present, no flush)
- Player A: `2c 3d` (no improvement)
- Player B: `4s 5h` (no improvement)
- **Expected:** both players' best five = T-J-Q-K-A (the board itself). True tie, pot split evenly. If the pot is an odd amount (e.g. 101 chips), the extra chip goes to whichever of A/B sits first left of the button (R31).

### TV-3 — kicker determines a clear (non-tied) winner (R4, R6)
- Board: `Ah Ad 7c 4s 2d`
- Player A: `Kc Qd` → best five: A-A-K-Q-7
- Player B: `Kh Jd` → best five: A-A-K-J-7
- **Expected:** A wins outright (Q kicker beats J kicker) — no split.

### TV-4 — 2-way all-in, main pot + one side pot (R27–R30)
Three players, no folds, all reach showdown.
| player | contributes | hole cards | best five |
|---|---|---|---|
| A | 100 (all-in) | `Ah As` | A-A-J-9-7 |
| B | 300 (all-in) | `Kh Kd` | K-K-J-9-7 |
| C | 300 (calls) | `Qh Qd` | Q-Q-J-9-7 |

Board: `2h 7d 9s Jc 4d`. C started with 500, has 200 uncommitted remaining in stack (not part of any pot).

- Main pot = 100 × 3 = **300**, eligible A, B, C → A wins (A-A beats K-K, Q-Q) → **A collects 300**.
- Side pot = (300−100) × 2 = **400**, eligible B, C only (A excluded, contributed only 100) → B wins (K-K beats Q-Q) → **B collects 400**.
- **Chip conservation check:** contributed 100+300+300=700; distributed 300+400=700. ✓. C keeps their uncalled 200. Final stacks: A=300 (net +200), B=400 (net +100), C=200+0=200 (net −300, i.e. lost the full 300 committed to this hand).

### TV-5 — 3-way all-in, main pot + two side pots (R27–R30)
Four players, no folds, all reach showdown.
| player | contributes | hole cards | best five | pair rank |
|---|---|---|---|---|
| A | 50 (all-in) | `Ac Ah` | A-A-K-9-6 | highest |
| B | 150 (all-in) | `Qc Qd` | Q-Q-K-9-6 | 2nd |
| C | 400 (all-in) | `Jc Jd` | J-J-K-9-6 | 3rd |
| D | 400 (calls, stack 1000) | `Th Td` | T-T-K-9-6 | lowest |

Board: `3h 6d 9c Ks 2s` (unpaired, no straight/flush possible).

- Pot 1 (main) = 50 × 4 = **200**, eligible A,B,C,D → best hand among all four = A (A-A) → **A collects 200**.
- Pot 2 = (150−50) × 3 = **300**, eligible B,C,D (A excluded) → best among these three = B (Q-Q) → **B collects 300**.
- Pot 3 = (400−150) × 2 = **500**, eligible C,D (A,B excluded) → best of these two = C (J-J) → **C collects 500**.
- D wins nothing from the pot but keeps 1000−400=**600** uncommitted.
- **Chip conservation check:** before: 50+150+400+1000=1600. After: A=200, B=300, C=500, D=600 → 1600. ✓.
- This is the key case for naively-flat pot-splitting code: if side pots aren't correctly nested by eligibility, this test catches it (D, the largest stack, must win **nothing** here despite calling the most).

### TV-6 — heads-up posting and action order (R17)
Two players, blinds 5/10, stacks 1000 each. Player A is on the button.
- Preflop: A (button/SB) acts **first** — calls to complete to 10. B (BB) checks (has the option). → flop.
- Flop: B acts **first**. A acts **last**. (Repeats on turn and river.)
- **Expected assertion:** action order is reversed pre- vs. post-flop in heads-up, unlike 3+-handed play where the same seat order (left of BB preflop, left of button postflop) holds every street.

### TV-7 — min-raise: short all-in does not reopen, doesn't reset the increment (R21–R25)
Blinds 5/10. Four players: UTG, Y, Z, BB(no action needed here for the point being tested).
1. UTG raises to 30 (increment = 30−10 = 20 ≥ 10 min → legal full raise). Last full raise increment recorded = 20.
2. Y goes all-in for 45 total (increment over 30 = 15 < 20 → **short all-in**, does not reopen, does not change the recorded increment).
3. Action returns to UTG, who already called/opened this round and already faced (and matched) the earlier bet: UTG may only **call the extra 15 (to 45) or fold** — UTG may **not** re-raise, since Y's raise was incomplete.
4. If instead player Z — who has **not yet acted** this round — faces Y's 45 all-in and wants to raise, Z's minimum legal raise is to **at least 65** (45 + the last full increment of 20), **not** 60 (45+15). A naive implementation that lets the short all-in set the new increment to 15 would wrongly allow a raise to 60.

### TV-8 — button movement is dead, not moving, when a seat empties (R14–R15)
4-max table, seats numbered 1–4, all occupied. Hand N: button=1, SB=2, BB=3 (seat 4 is UTG).
Between hand N and N+1, the player in **seat 2** leaves.
- New BB seat = next occupied seat after old BB(3) → seat 4.
- New SB seat = seat immediately before seat 4 → seat 3 (occupied — normal SB, seat 3 was BB last hand, correctly becomes SB this hand).
- New button = seat immediately before seat 3 → **seat 2, which is empty** → the button is **dead** (a positional marker at an empty seat; nobody posts or acts as "the button" itself beyond marking rotation).
- **Expected:** seat 1 is UTG this hand (first to act preflop, having been button last hand) — no active player skipped or double-posted a blind despite seat 2's departure. A naive **moving-button** implementation would instead hand the button to seat 3 or 4 directly, causing seat 1 to miss its natural big-blind turn — this is exactly the exploit R14 is written to close.

### TV-9 — timeout and sit-out (R32–R35)
- **TV-9a:** Player faces a bet of 20 (no check available), takes no action for 30s. **Expected:** engine auto-folds them, marks them sitting out.
- **TV-9b:** Player faces no outstanding bet (check is legal), takes no action for 30s. **Expected:** engine auto-checks for them, **and still** marks them sitting out (R33 applies regardless of which default action resulted).
- **TV-9c:** A now-sitting-out player is dealt into the next hand, posts their blind if due, and is auto check-or-folded on every turn without further timers elapsing (no second 30s wait per action while sitting out — the sit-out state itself is what drives the auto-action, not a fresh timer each time). **Note:** this "no repeated timer while sitting out" behaviour is an implementation-efficiency inference from R35, not separately stated by the owner — flagged as an **engine decision**, low-risk.

## open questions
- **Not an owner decision, flagged for later planning:** the muck-vs-verifiability tension described under "protocol fit" above — a mucked hand may never get its hole cards revealed anywhere, which limits what a future verifiable-shuffle scheme can prove about hands that end in a muck rather than a shown showdown. Relevant to [[v3_0_provable_randomness]], not blocking here.
- **Not yet decided, low priority:** which of the two join mechanisms (R41a/b) becomes the default once playtested; this spec deliberately specifies both without picking, per the initiative.
- **Assumption, not escalated:** no manual "sit out" toggle exists in v1.0 (R38); no seat-release destination for a table dropping to 0 players (R13) beyond "matchmaking may reclaim it."

## references
- Robert's Rules of Poker, v11 — [pagat.com/docs/RobsPkrRules11.pdf](https://www.pagat.com/docs/RobsPkrRules11.pdf) (retrieved 2026-09-23; primary text not fully machine-readable at retrieval time, see verification caveat in "purpose").
- Robert's Rules of Poker for Private Games (mirror) — [github.com/mhgerov/roberts-rules-of-poker-for-private-games](https://github.com/mhgerov/roberts-rules-of-poker-for-private-games) (retrieved 2026-09-23).
- Poker Tournament Directors Association Rules — [pokertda.com/view-poker-tda-rules](https://www.pokertda.com/view-poker-tda-rules/), full PDF: [Poker-TDA-Rules-2015-Version-1.0](https://www.pokertda.com/wp-content/uploads/2011/01/Poker-TDA-Rules-2015-Version-1.0-full-longform-PDF-1.pdf) (retrieved 2026-09-23).
- PokerNews, "Five Poker Rules You Didn't Know Existed" (odd-chip convention) — [pokernews.com](https://www.pokernews.com/strategy/five-poker-rules-you-didnt-know-existed-26946.htm) (retrieved 2026-09-23).
- [[v1_0_casual_multiplayer_poker]] — product scope and owner decisions this spec implements.
- [[0003_monorepo_structure_and_tech_stack]] — hand-history sink, in-memory state (relevant to R47, R53).
- [[v3_0_provable_randomness]] — where the "protocol fit" section's open items get resolved.
