---
type: spec
status: review
area: ui
created: 2026-09-23
updated: 2026-09-24
tags: [spec, ui, design-system]
---

# design_system_ground_rules

<!-- status: draft | review | stable | deprecated -->

## purpose
**Design frozen 2026-09-24 (owner):** the Claude Design system [Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy) as of 2026-09-23 07:30 UTC is the v1.0 visual reference. No more Claude Design rounds; everything under *known gaps* below is fixed while porting it to code ([[0004_design_system_in_code]]).

Ground rules for the product's design system and the brief that hands it to **Claude Design** (owner's tool of choice, 2026-09-23), which prototypes the direction and holds the tokens and components. `apps/web` (and a future `packages/ui`) is built from the result. Initiative: [[ui_design_system]]. Research: [[poker_ui_competitors_and_table_layouts]], [[ui_visual_foundations]].

**Owner choices (2026-09-23):** direction **C · Modern Felt, sharpened** from the [Table directions](https://claude.ai/artifact/SyXGGC8R5Xbpbbm33mtZxg) page; **four-colour deck on by default** (red/black as a setting); **dark theme only for v1.0**, with tokens structured so a light theme can be added later without redesign.

The requirements below are the *fixed* layer: later versions (v1.1 lobbies, v2.0 wallet, v3.0 verification) add components and semantic roles on top of them; changing one of them needs an ADR. Reviewed by `design-reviewer` on 2026-09-23 (verdict *approve with changes*; all findings applied in this revision).

## requirements

### identity & scope
- **UI-R1** The v1.x product MUST NOT look or read like money: no currency symbols, coins or coin-like discs, gold/brass, wallet or bank imagery, and none of the words *buy-in, cash, cash out, deposit, withdraw, wallet, balance, bankroll, real money*. *Chips*, *play chips*, *stack*, *top up* and *blinds* are allowed.
- **UI-R2** Before v3.0 the UI MUST NOT claim or imply fairness: no "provably fair/random", shields, seals or check-marks attached to the shuffle or deal. v2.0 copy MUST say the shuffle is run by the server.
- **UI-R3** The visual direction is flat and contemporary: MUST NOT use casino signifiers — gold trim, wood rails, gloss or bevels, neon, sparkle/particle effects, slot-machine or wheel motifs.
- **UI-R4** In v1.0 a bet is shown as **a number plus one flat, non-circular marker**; there are no chip-denomination tiers or chip stacks.

### colour
- **UI-R5** Every text/background pair MUST meet WCAG 2.2 AA: ≥ 4.5:1 for text, ≥ 3:1 for text ≥ 24px (or ≥ 18.66px bold), and ≥ 3:1 for meaningful UI boundaries, icons and focus indicators (SC 1.4.3, 1.4.11). Ratios are computed with the WCAG 2.x formula **on the composited result** (after any transparency), not judged by eye. APCA may inform judgement but MUST NOT be cited as compliance.
- **UI-R6** Dimmed states (folded, disabled, open seat) MUST NOT be made with element opacity; they use their own tokens, which satisfy UI-R5.
- **UI-R7** Colour MUST NOT be the only signal for: suit, whose turn it is, folded, all-in, sitting out, reconnecting, winner, or danger. Each also has a word, an icon, a shape or a position.
- **UI-R8** Tokens MUST be three-tiered: primitives (raw scales, preferably OKLCH-derived) → semantic roles (surface, text, border, accent, danger, focus, …) → component tokens. Components use semantic or component tokens only, never primitives or literals.
- **UI-R9** Deck: four-colour by default (spades near-black, hearts red, diamonds blue, clubs green) with a red/black setting. Rank and suit glyph MUST appear together at every card size. Every suit colour MUST reach ≥ 4.5:1 on the card face. Coloured suits SHOULD differ from each other by ≥ 1.3:1 in luminance and by ΔE_OK ≥ 10 under protanopia, deuteranopia and tritanopia simulation (the starting set fails this for ♥/♣ under deuteranopia and ♦/♣ under tritanopia — iterate).
- **UI-R10** Danger MUST differ from the accent by ΔE_OK ≥ 15 under normal vision and under each simulation above, and always carries an icon and a word. Danger has its own on-danger text token.
- **UI-R11** The accent is reserved for *your turn* and the primary action (turn ring, raise/bet button). Card backs, bet markers and decoration MUST NOT use it. The keyboard focus ring MUST be distinct from the turn ring.

### type & numbers
- **UI-R12** Every number that changes in place (stack, bet, pot, timer, hand number) MUST use tabular lining figures.
- **UI-R13** Fonts MUST be licensed for web embedding (OFL or equivalent) and self-hostable. Starting pair: **Barlow** (UI) and **Barlow Semi Condensed** (seat labels, buttons, headings) — both verified to ship `tnum` (Google Fonts files, checked 2026-09-23).
- **UI-R14** Every glyph on the table — including badges, the dealer button and card indices — MUST render at ≥ 11px. Face-up cards at seats show the rank at ≥ 13px bold and the suit at ≥ 11px.
- **UI-R15** Nicknames are at most 16 characters (product-wide). Nickname + stack MUST fit a seat at 9-max on the reference viewport; nicknames truncate with an ellipsis, stacks never truncate.
- **UI-R16** Chip amounts show in full with thousands separators up to 99,999 and abbreviate above that (e.g. 124.5k, rounded down) on seats, bet markers and the pot. Action buttons, the bet-sizer input and amount-change notices MUST always show the exact full number.

### layout & interaction
- **UI-R17** Phone portrait is the primary layout. The table surface is drawn at a **360×640 reference** and MUST be scaled uniformly to fit the width and the height left after the safe-area insets, centred, with the top bar and action bar full-width; the table never scrolls. Every requirement below that names 360×640 is measured on that reference drawing. Landscape/desktop is a second layout of the same components, not a different design.
- **UI-R18** At 9-max on the reference viewport, all 9 seats with a bet out, a full 5-card board and a multiway showdown with face-up seat cards MUST be shown without overlap, and seats sit ≥ 8px from the screen edge.
- **UI-R19** The action bar (fold / check / call / bet / raise and bet sizing) sits in the bottom thumb zone. Primary action targets are ≥ 44×44px; no target is smaller than 24×24px (WCAG 2.5.8).
- **UI-R20** Fold MUST be separated from Check/Call by a gap of ≥ 16px or distinct placement.
- **UI-R21** Bet sizing offers presets first, then a slider and a typed amount. Postflop presets: ½ pot, ¾ pot, pot, all-in, where a fraction-of-pot raise is **raise-to = highest bet this street + fraction × (pot + to-call)**, with pot including every bet already on the table. Preflop presets are big-blind multiples (e.g. 2.5×, 3×, 4×). Presets below the minimum raise are disabled. The primary button always reads **"Bet X"** or **"Raise to X"** with the exact amount.
- **UI-R22** An action button MUST act on the table state the player saw: if the state changes under the player's finger (e.g. a raise arrives), the bar re-renders with the new amounts and does not apply the stale tap.
- **UI-R23** The turn timer shows remaining time as digits as well as a ring or bar, stays legible with motion reduced, and switches to an urgent style (with a word or icon, not only colour) at ≤ 10 seconds. It stays visible whenever it's your turn, including while the bet sizer or any sheet is open.
- **UI-R24** A seat that is reconnecting or sitting out shows the remaining hold/grace time as a visible countdown (one clock for both, per [[nlhe_cash_game_rules]] R36).
- **UI-R29** Table overlays MUST NOT cover seats: banners live in the action-bar slot or the top bar; pots use at most two rows of pills, beyond which they collapse to "Main X · +N side pots" and expand on tap; a seat's tray (dealer, bet, shown cards) wraps inside its own column.

### motion
- **UI-R25** Motion is informational (deal, bet to pot, pot to winner, turn change) and short (≤ 300ms for UI state changes; longer only for travel across the table). Under `prefers-reduced-motion: reduce`, transforms are replaced by fades or instant changes.
- **UI-R26** Nothing flashes more than 3 times per second.

### accessibility beyond colour
- **UI-R27** Cards, seats and actions have screen-reader names (e.g. "Queen of diamonds", "tomas.v, 850 chips, bet 60"). When it's the player's turn, it is announced in a polite live region and focus moves to the action bar; focus is always visible. Keyboard shortcuts MUST need a modifier or be off by default (WCAG 2.1.4).
- **UI-R28** At 200% text scaling the action bar remains fully usable: type sizes are in rem, and at ≥ 150% text Fold moves to its own row; labels never clip.
- **UI-R30** The player's own hole cards MUST stay visible while they act, at every text size (if the action bar grows over them, the bar repeats them).
- **UI-R31** The table surface MUST be laid out inside the device safe-area insets (notch, home indicator), not only the bars.
- **UI-R32** Pinch/page zoom MUST NOT be disabled (no `user-scalable=no` or `maximum-scale`).
- **UI-R33** The table surface is a fixed drawing whose seat labels need not grow with text size, **provided** tapping a seat opens a sheet with that seat's full name, exact stack, bet, state and countdown at full text size (owner, 2026-09-23). At 200% text the action bar may cover the lower seats (owner-accepted), as long as UI-R30 holds.
- **UI-R35** Every sheet and dialog MUST move focus into itself on open, keep focus inside while open, close on Escape, and return focus to what opened it.
- **UI-R36** A sheet opened during the player's own turn (e.g. seat details) MUST NOT cover the turn timer, the player's hole cards or the action bar; it sits above the bar and the actions stay usable. Open overlays (side-pot list, seat sheet) close when the player's turn starts.
- **UI-R34** The signature element is the **pointer tab**: every bet is a number plus a flat pentagon pointing at the pot, turning around with the word "returned" for an uncalled bet (owner, 2026-09-23).

## design

### direction C, sharpened
The card room, redrawn flat: the table outline and deep green-teal felt every poker player recognises, stripped of casino gold, wood and gloss. Condensed type, pill-shaped seats and buttons, a coral accent kept for your turn and the primary action. "Sharpened" means C gets **one signature element** that makes it recognisably ours instead of generic green felt — the owner chose the **pointer tab** bet marker (UI-R34) from Claude Design's three options.

### starting palette (dark; Claude Design refines within the rules)
| role | value | computed contrast |
|---|---|---|
| ground | `#0A1614` | — |
| felt | `#0F3D37` | — |
| felt edge | `#1E5E54` | — |
| seat | `#0E2622` | — |
| seat, folded | `#0C1F1C` (text `#A3B8B2`, muted `#8FA8A1`) | 8.2 / 6.7 : 1 |
| button | `#14302B`, border `#2B524B` | — |
| text | `#EEF5F2` | 14.4 on seat · 10.9 on felt |
| text muted | `#9FBDB5` (`#A9C7BF` on felt) | 7.9 on seat · 6.7 on felt |
| accent (coral) | `#FF8A65`, on-accent `#221008` | 5.2 on felt · 7.9 on-accent |
| danger (candidate) | `#B8A1FF`, on-danger `#1A1030` | 7.3 on seat · 5.5 on felt · 8.3 on-danger |
| focus ring | `#EEF5F2`, 2px + 2px offset | 10.9 on felt · 16.7 on ground |
| card face | `#FFFFFF` | — |
| suits | ♠ `#101816` · ♥ `#C41E3A` · ♦ `#1A5FCC` · ♣ `#16753D` | 18.0 / 5.8 / 5.9 / 5.8 on white |

Known weak spots to fix in Claude Design: suit separation under colour-vision simulation (UI-R9 SHOULD); seat border on felt (1.13:1) and fold-button border on ground (2.12:1) — acceptable only because their labels carry the meaning.

### game-state roles (semantic layer, v1.0)
`felt`, `felt-edge`, `seat`, `seat-acting`, `seat-folded`, `seat-away` (sitting out / reconnecting), `seat-open`, `seat-winner`, `pot`, `bet-marker`, `card-face`, `card-back`, `suit-spade|heart|diamond|club`, `timer-track`, `timer-fill`, `timer-urgent`, `accent`, `on-accent`, `danger`, `on-danger`, `focus`, `allin`.

## brief for Claude Design
<!-- paste everything inside the fence into Claude Design; it is self-contained -->

```text
DESIGN SYSTEM BRIEF — poker web app, v1.0 ("Modern Felt, sharpened")

PRODUCT
A mobile-first web app for No-Limit Texas Hold'em with free play chips. Strangers type a
nickname (max 16 characters), pick a blinds level (1/2, 5/10 or 25/50) and are seated at a
public 9-seat table. Everyone sits with 100 big blinds; free top-up to 100bb between hands
(not offered at 100bb or more). 30-second turn timer (on timeout: check if possible, else fold,
and the player is marked sitting out). Sitting-out players are still dealt in, post blinds and
are auto-checked/folded. Disconnected players keep their seat ~2 minutes. No accounts, no
spectators, no money.
Later versions (not now, but the system must leave room): private lobbies for friends in one
room (v1.1), a crypto wallet on testnet (v2.0), a hand-verification screen (v3.0).

DIRECTION
"The card room, redrawn flat." Keep what every poker player recognises — a table outline and a
deep green-teal felt — and remove the casino: no gold, brass, wood, gloss, bevels, neon,
sparkles, wheels. Flat fills, pill-shaped seats and buttons, condensed labels, a coral accent
used ONLY for "your turn" and the primary action.
Dark theme only for v1.0; structure tokens so a light theme can be added later.
Give it ONE signature element so it doesn't read as generic green felt. Propose 2–3 options
(e.g. a distinctive card-back pattern, a table-edge treatment, or the bet/pot marker) and show
each on the table screen.

HARD RULES
1. No money look or words: no currency symbols, coins or coin-like discs, gold, wallets; never
   "buy-in, cash, cash out, deposit, withdraw, wallet, balance, bankroll". "Chips", "stack",
   "top up", "blinds" are fine. A bet is a number plus ONE flat, non-circular marker; no chip
   stacks or denomination colours.
2. No fairness claims or badges (no "provably fair", shields, seals, check-marks by the deck).
3. WCAG 2.2 AA everywhere, computed with the WCAG 2.x formula on the composited colours:
   text ≥ 4.5:1 (≥ 3:1 at 24px+ or 18.66px bold); UI boundaries, icons and focus ≥ 3:1.
   Show the ratio for every text/background token pair. Never dim with opacity — folded,
   disabled and open-seat states get their own tokens that pass.
4. Never colour alone for: suit, whose turn, folded, all-in, sitting out, reconnecting,
   winner, danger. Add a word, icon or shape.
5. Four-colour deck by default: spades near-black, hearts red, diamonds blue, clubs green;
   each ≥ 4.5:1 on the card face; rank + suit glyph always together. Red/black is a setting.
   Try to keep the coloured suits apart under protanopia, deuteranopia and tritanopia.
6. Danger must be clearly different from the coral accent, including for colour-blind users
   (not another red/pink/orange), and always carries an icon and a word. Starting candidate:
   #B8A1FF with dark text #1A1030. Card backs and bet markers must not use the accent. The
   keyboard focus ring must look different from the turn ring.
7. Tabular figures for every changing number (stacks, bets, pot, timer, hand #). Amounts in
   full up to 99,999 with separators, then abbreviated (124.5k).
8. Phone portrait first. Reference = 360×640 CSS px layout viewport (the space inside a mobile
   browser). All 9 seats + board + pot + your cards + action bar fit with no scrolling; action
   bar pinned to the bottom. Must work with all 9 seats having a bet out, a full 5-card board,
   and a multiway showdown with face-up cards at seats — no overlaps, seats ≥ 8px from the
   screen edge. Nickname + stack fit every seat; nicknames truncate, stacks never do.
   Every glyph ≥ 11px (badges, dealer button and card indices included); face-up seat cards:
   rank ≥ 13px bold, suit ≥ 11px.
9. Action bar at the bottom (thumb zone). Primary buttons ≥ 44×44px; nothing < 24×24px.
   Fold separated from Check/Call by ≥ 16px or placed apart.
10. Bet sizing: presets first, then slider and typed amount. Postflop presets ½ pot, ¾ pot,
   pot, all-in ("pot" = pot-sized raise). Preflop presets are big-blind multiples (2.5×, 3×,
   4×). Presets below the minimum raise are disabled. The main button always says
   "Bet X" or "Raise to X" with the exact number.
11. If the amounts change while it's your turn (someone raises), the action bar visibly
   updates; a tap never applies to the old amounts.
12. Turn timer = digits + a ring or bar; readable without animation; urgent style at ≤ 10s
   with a word or icon, not only colour. Reconnecting / sitting-out seats show a countdown.
13. Motion only to inform (deal, bet → pot, pot → winner, turn change), ≤ 300ms for state
   changes; show the reduced-motion variant. Nothing flashes more than 3 times per second.
14. Accessibility: name every card, seat and action for screen readers (e.g. "Queen of
   diamonds", "tomas.v, 850 chips, bet 60"); visible focus; the action bar still works at
   200% text size.

STARTING TOKENS (refine them; keep roles)
ground #0A1614 · felt #0F3D37 · felt-edge #1E5E54 · seat #0E2622 · seat-folded #0C1F1C
(text #A3B8B2, muted #8FA8A1) · button #14302B / border #2B524B · text #EEF5F2 · text-muted
#9FBDB5 · accent (coral) #FF8A65, on-accent #221008 · danger #B8A1FF, on-danger #1A1030 ·
focus #EEF5F2 (2px ring, 2px offset) · card face #FFFFFF · suits ♠ #101816 ♥ #C41E3A
♦ #1A5FCC ♣ #16753D.
Type: Barlow (UI text) + Barlow Semi Condensed (seat labels, buttons, headings), both have
tabular figures. Radius: seats/buttons pill, cards ~7px.
Token structure: primitives (OKLCH scales) → semantic roles → component tokens. Semantic
game roles to include: felt, felt-edge, seat, seat-acting, seat-folded, seat-away, seat-open,
seat-winner, pot, bet-marker, card-face, card-back, suit-*, timer-track, timer-fill,
timer-urgent, accent, on-accent, danger, on-danger, focus, allin.

COMPONENTS (with all states)
Card (face / back / empty slot; sizes: seat mini, face-up at seat, board, your hand) ·
Bet marker · Seat (open, waiting, in hand, acting + timer, folded, all-in, sitting out +
countdown (still dealt in, auto-folded), reconnecting + countdown, winner, cards shown,
cards mucked) · Dealer button, small-blind and big-blind markers · Board · Pot (main + side
pots, split pot, uncalled bet returned) · Action bar — each set: check / bet; fold / call /
raise; fold / call all-in (can't raise); fold / call only (raise not reopened by a short
all-in); big blind's option (check / raise); not your turn · Bet sizer (presets, slider,
typed amount) · Turn timer (normal / urgent) · Top bar (blinds, hand #, leave, settings) ·
Settings sheet (four-colour / red-black deck) · Button · Text field · Segmented control ·
Sheet/dialog · Banner/toast · Loading state.

SCREENS TO PROTOTYPE (phone 360×640 unless noted)
1. Start: nickname + blinds level picker → "Find a table", and its "Finding a table…" state.
2. Seated alone: table waits for a second player.
3. Joined mid-hand: choose "Post big blind now" or "Wait for the big blind".
4. Hand in progress, someone else acting; show blinds markers and the dealer button.
5. Your turn on the turn: call 60 into pot 120, 18s left, 9 seats in mixed states (folded,
   sitting out, reconnecting, open seat) — plus the bet sizer expanded.
6. Your turn preflop with big-blind-multiple presets; and the big blind's option.
7. Facing a short all-in where you can only call or fold.
8. All-in with a side pot; then a split pot.
9. Showdown, multiway: face-up cards at seats, winner shown, loser mucks; pot to winner.
   Also: winning without a showdown (everyone else folded) and an uncalled bet returned.
10. You timed out and are sitting out: "I'm back" + removal countdown.
11. You lost connection: reconnecting banner.
12. Between hands: top up to 100 big blinds (and the stack ≥ 100bb case, where it's hidden).
13. Leave table: confirm. Between hands you just leave. Mid-hand, leaving counts as a
    disconnect: your hand is checked or folded automatically, and if you don't come back
    within ~2 minutes your seat and chips are gone (nothing carries over).
14. Hand cancelled — everyone's chips returned to their start-of-hand stacks (rare error
    case); and "table closed" after a server restart.
15. Heads-up (2 seats: the button posts the small blind) and 6 seats; dead button over an
    empty seat.
16. Settings sheet with the deck toggle.
17. Desktop/landscape version of screen 5.
```

## review log
- **2026-09-23, round 1** — `design-reviewer` on the spec + brief: *approve with changes*; applied in the brief before it went to Claude Design.
- **2026-09-23, round 2** — `design-reviewer` on the Claude Design output [Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy): *approve with changes*. Contrast (76 pairs + 20 more), suits (ΔE_OK ≥ 12.5 across simulations), vocabulary and 9-seat geometry verified by local render. Three high findings (200% text, timer hidden in sizer, abbreviated amounts on buttons) and seven medium; requirements UI-R16, R21, R23, R24, R27, R28 tightened and UI-R29 added. Fix list below goes back into Claude Design.

### fixes for Claude Design (round 2)
<!-- paste the fenced block into Claude Design -->

```text
REVIEW FIXES — Modern Felt, round 2 (keep everything else as is)

HIGH
1. 200% text: all type tokens in rem, not px. At ≥150% text the action bar stacks Fold on its
   own row above Call / Raise; no label may clip or overlap ("Raise to 99,999" must fit).
   Make the 200% example in ActionBar actually scale.
2. Bet sizer open: it must show the 4px turn-timer bar and "Your turn · 18s", including the
   urgent (≤10s) style.
3. Exact amounts: action buttons, the sizer's typed field and the "amounts updated" notice
   always show full digits ("Raise to 124,567"). Abbreviation (124.5k) only on seats, bet
   markers and the pot.

MEDIUM
4. Pot-fraction formula: raise-to = highest bet this street + fraction × (pot + to-call),
   where pot includes every bet already on the table. Fix BetSizer/README and the numbers in
   S05 / ActionBar examples.
5. Side pots: at most two rows of pot pills; beyond that show "Main X · +N side pots" and
   expand on tap. Add a stress test with 8 side pots.
6. Banners must not cover seats: move table banners (S11 reconnecting, S14 errors) into the
   action-bar slot or the top bar.
7. Showdown (S09): losing hands are mucked automatically. Remove tomas.v's shown 9♥9♣, or
   show it only as an optional voluntary "Shows" state labelled as such.
8. Sitting out (S10): one clock with disconnect, ~2 minutes — "Seat released in 1:52", not 4:32.
9. Accessibility: remove role="application" from Table; implement the polite live region
   ("Your turn. To call 60, pot 120. 18 seconds.") and move focus to the action bar on your
   turn; desktop shortcuts (S17) need a modifier or are off by default.
10. Seat trays: wrap to a second line and stay inside the seat's column (R1 seat with
   dealer + shown cards + "returned" bet currently overlaps the hero's bet).

LOW
- Urgent ring: make it actually dashed (inline strokeDasharray overrides the CSS).
- text-muted on felt-edge is 4.08:1 — keep "returned" and other text off the rail, or add a
  token that passes.
- All-in's 2px white border looks like the focus ring: change one (e.g. all-in 3px or inset).
- Copy: S01 "table size" → "blinds"; S13 say the chips aren't kept if you don't come back
  within ~2 minutes (leaving mid-hand counts as a disconnect); S03/S12 drop "free".
- Delete the unused trophy icon. Reduced motion: stop the spinner and loading dots moving.
- Add env(safe-area-inset-*) padding to the top bar and action bar.

SIGNATURE
- Use A · Pointer tab as the signature (pending owner's final pick).
```

### round 3 (2026-09-23) — re-check of the round-2 fixes
`design-reviewer`, rendered in headless Chrome at 360×640: *approve with changes*. All 3 high and 7 medium round-2 fixes verified; stress tests, contrast (tokens unchanged) and vocabulary clean. New: at ≥ 150% text the taller action bar covers the hero's hole cards (→ UI-R30); the table drawing ignores safe-area insets, so on a notched phone the bars cover top seats and the hero seat (→ UI-R31). Lows: preflop 2.5× presets don't round (62.5 at 25/50); a capped "Pot" duplicates "All-in" without saying so; the expanded side-pot list overlaps the hero's cards. Owner decisions (2026-09-23): signature **A · pointer tab**; **add** the tap-a-seat sheet; bar covering lower seats at 200% text **accepted** → UI-R33, R34.

```text
REVIEW FIXES — Modern Felt, round 3 (keep everything else as is)

1. Large text: at ≥150% text, when the action bar grows over your seat, repeat your two
   hole cards (seat size) in the bar's "Your turn" line — as the bet sizer already does.
   Your cards must stay visible while you act at any text size.
2. Safe area: lay out the whole 360×640 table surface inside the safe-area insets, not only
   the top bar and action bar. Show a notched-phone example (47px top, 34px bottom).
3. Preflop presets: round to a whole chip (2.5× at 25/50 → 63 or 60 — pick a rule and state
   it; the minimum raise still wins).
   [note 2026-09-24: the 62.5 example was wrong — at 25/50 the big blind is 50, so 2.5× = 125;
   all v1.0 blinds give whole multiples. Claude Design added nearest-chip rounding anyway.]
4. When a preset is capped at your stack (e.g. "Pot" = 200 = all-in), label it "All-in 200"
   instead of showing two identical buttons.
5. The expanded side-pot list must not cover your hole cards (open it as a sheet, or
   upward).
6. Pinch zoom stays enabled: no user-scalable=no or maximum-scale in the viewport meta.
7. Tap a seat → the existing Sheet at full text size with the seat's full name, exact
   stack, bet, state and countdown (reuse the seat's accessible label).
8. A · Pointer tab is the signature (owner, final). Remove B and C from the Table options
   and the Signature options page; keep a short note of why A was chosen.
```

### round 4 (2026-09-24) — spot-check of round 3
`design-reviewer`, headless render: *approve with changes*. Verified: hole cards in the large-text bar, preset rounding + All-in merge, upward side-pot list, viewport meta (all 42 previews), B/C signatures removed, no regressions (stress tests, contrast, vocabulary). Open: SeatSheet covers the whole action bar during your turn and has no focus handling/Escape (→ UI-R35, R36); the safe-area layout adds insets on top of 360×640, so a 360×640 screen with a notch scrolls and the action bar falls off-screen (→ UI-R17 reworded: scale to fit).

*Not sent — superseded by the design freeze; carried into* known gaps *below.*

```text
REVIEW FIXES — Modern Felt, round 4 (not sent)

1. Seat details during your turn: the SeatSheet must not cover the turn timer, your hole
   cards or the action bar. Open it above the bar (shorter sheet, or a popover by the
   seat); Fold / Call / Raise stay usable. Add an S19 example with the action bar live.
2. Sheets and dialogs (all of them): move focus into the sheet on open, keep it inside,
   close on Escape, return focus to the seat/button that opened it.
3. Overlays close when your turn starts (side-pot list, seat sheet).
4. Fit, never scroll: scale the 360×640 table surface uniformly to fit the width and the
   height left after the safe-area insets, centred; top bar and action bar stay full width.
   Show it on a 360×640 screen with 47/34 insets and on 390×844.
5. README: fix the rounding example (2.5 × 25 isn't a v1.0 blind — say every v1.0 multiple
   is whole: 5, 25, 125).
```

## known gaps — fix in implementation
What the frozen Claude Design reference does **not** do correctly, found by `design-reviewer` rounds 2–4. Each item is fixed while porting to `packages/ui` ([[0004_design_system_in_code]]) and gets a test; the port isn't done until this list is empty.

**Behaviour the reference gets wrong**
1. **Seat details sheet covers the action bar during your turn** — timer, hole cards and Fold/Call/Raise hidden while the clock runs (UI-R36). Open it above the bar; actions stay usable.
2. **Sheets and dialogs don't manage focus** — focus doesn't move in, isn't trapped, Escape doesn't close, `aria-modal` is set anyway (UI-R35). Applies to every Sheet.
3. **Overlays stay open when your turn starts** — side-pot list and seat sheet (UI-R36).
4. **Safe-area layout scrolls** — insets are added *on top of* 360×640, so a 360×640 screen with a notch scrolls and the action bar falls off-screen; on wider phones the table is left-aligned, not centred (UI-R17: scale the table surface to fit, centred, never scroll).
5. **Preset and minimum-raise maths live in the UI bundle** (`Felt.potPresets`, `preflopPresets`) — move the rules to an engine query (`legalActions`) and compute presets from it (UI-R21, [[nlhe_cash_game_rules]] R20–R26).

**Rules the reference only states, so code must enforce**
6. `text-muted` on `felt-edge` is 4.08:1 — nothing muted may sit on the rail (tray text uses `text`). Needs a test, not just a README line.
7. Coloured suits: ♥/♦ luminance under protanopia is 1.00:1 (UI-R9 SHOULD unmet); ΔE separation passes but is near the limit — keep the suit shapes mandatory and don't retune colours without re-running the simulation.
8. Table-surface labels don't grow with text size (accepted under UI-R33) — the SeatSheet is the compliance path, so it must be reachable from every seat, by tap and keyboard.

**Documentation errors in the reference**
9. README rounding example "2.5 × 25 = 62.5 → 63" uses a big blind that doesn't exist in v1.0 (all v1.0 multiples are whole: 5, 25, 125).

**Never verified by any review — test during implementation**
10. Real screen-reader output (VoiceOver iOS, TalkBack Android) — the live region and names were only read from code.
11. Viewports: 320px-wide phones, screens shorter than 640 after insets, landscape phone, desktop layout in depth.
12. 200% text combined with notch insets; motion timings and reduced-motion variants in a running app.
13. The Claude Design `tokens.css` generator wasn't available — the port generates its own from `tokens.json`; contrast tests run on the generated CSS.

## threat model / failure modes
Not a fairness spec; the relevant failure modes are *player* harm, not cheating:
- mis-taps (Fold vs Call, stale-state taps, ambiguous presets) → UI-R19–R22
- unreadable state (whose turn, all-in, cards) under time pressure or colour-vision deficiency → UI-R5–R7, R9, R10, R12, R14, R23
- controls off-screen in a real mobile browser → UI-R17, R18
- the UI implying money or fairness the product doesn't have → UI-R1, R2, R4 (regulatory and trust risk; see [[roadmap]] "what each version is not")

## verification
- Contrast: every token pair computed on composited colours (script or Claude Design's own check) and listed; `design-reviewer` re-computes.
- Colour-vision: suits, accent/danger and state colours checked under protanopia, deuteranopia and tritanopia simulation (Machado 2009), ΔE_OK reported.
- Layout: the prototype's 9-seat worst case (UI-R18) at 360×640; later, automated screenshot tests at 360×640 / 390×844 / 768 / 1280 in `apps/web` (set in the code-side ADR).
- Vocabulary: manual copy review against UI-R1/R2 before any release (as in [[v1_0_casual_multiplayer_poker]]).
- `design-reviewer` pass on the Claude Design output before this spec moves to `review`.

## open questions
- **Pre-actions** ("check/fold", "call any" boxes while waiting): common in online poker, not in v1.0 scope. Left out unless the owner adds them — UI-R22 applies if they are.
- **Top-up: opt-in or automatic?** still open in [[awaiting_owner_review]]; screen 12 assumes opt-in.
- **Signature element** — owner picks from Claude Design's 2–3 options.
- **Suit colours** that pass UI-R9's SHOULD under all three simulations — none found yet; iterate in Claude Design.
- **Nickname limit** of 16 characters is a design proposal; confirm with the engine/server (no limit is set in [[nlhe_cash_game_rules]]).

## references
[[ui_design_system]] · [[poker_ui_competitors_and_table_layouts]] · [[ui_visual_foundations]] · [[nlhe_cash_game_rules]] · [[v1_0_casual_multiplayer_poker]] · [Table directions](https://claude.ai/artifact/SyXGGC8R5Xbpbbm33mtZxg)
