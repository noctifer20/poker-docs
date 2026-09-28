---
type: research
topic: first player feedback on the live v1.0 build (table look, motion, sound, pace, bet readability)
source: owner, relaying private testers of https://poker.noctifer20.com (number of testers and devices not recorded)
retrieved: 2026-09-28
created: 2026-09-28
tags: [research, feedback, playtest, ui, design-system]
---

# playtest_feedback_2026_09_28

## summary
First feedback from real players on the live v1.0 build (`../poker-monorepo` `main` at `fda7539`), relayed by the owner on 2026-09-28. Five points, all about how the table *feels and reads* — none about rules or bugs. Several touch decisions the owner made when the design was frozen ([[design_system_ground_rules]], 2026-09-24), so they are queued in [[awaiting_owner_review]] rather than turned straight into work. Tasks live in [[ui_design_system]].

## key points
Feedback as given by the owner (wording kept):

| id | feedback |
|---|---|
| F1 | too little visuals: chips, animations; cards are a bit small; no dealing animation, everything comes to the screen instantly |
| F2 | no music |
| F3 | a bit too fast |
| F4 | table is green as well as the players, so it's hard to differentiate |
| F5 | not exactly clear who put what amount of money; probably needs animation and more highlight |

Not recorded: how many players, which devices/screen sizes, how many hands. Worth asking next time — F1 (card size) and F4 depend on the screen.

## implications for poker
What the spec and the code say today, per point. Code facts checked on `fda7539` on 2026-09-28.

| id | spec today | code today | what stands in the way |
|---|---|---|---|
| F1 chips | **UI-R4**: a bet is a number plus one flat, non-circular marker; no chip stacks or denominations. **UI-R1**: no coins or coin-like discs. | follows the spec | chip visuals contradict UI-R4 as written → owner decision |
| F1 animation | **UI-R25** names four informational motions: deal, bet → pot, pot → winner, turn change | only one exists: a 200ms deal animation in `packages/ui` (`motion.css`, `Table.tsx`), switched on by `apps/web` `TableScreen.tsx`. Bet → pot, pot → winner, turn change and board reveal are not implemented. Testers still saw "no dealing animation", so the existing one is either too short to notice or not firing — **unverified**. | mostly a gap between spec and code, not a design change. Spec *known gap 12* already lists "motion timings in a running app". UI-R25 caps state changes at ≤ 300ms; travel across the table may be longer. |
| F1 card size | **UI-R14** sets minimum glyph sizes, not card sizes; the owner accepted ≈9.4px glyphs at 320px (2026-09-25) | follows the frozen reference | bigger cards compete for space with 9 seats on a phone (UI-R29, UI-R33) → design work |
| F2 music | nothing — sound is not mentioned in the spec or in v1.0 scope | no audio code | new scope → owner decision |
| F3 pace | nothing in the spec | the server's only pacing delay is **3s between hands** (`HAND_PAUSE_MS`, env-configurable). No pause between streets or before/after showdown; opponents' actions appear the moment they arrive. | unclear *which* part feels fast → owner question. Probably linked to F1/F5: with no animation, state changes are instant. **Assumption**, not tested. |
| F4 green on green | palette: felt `#0F3D37`, seat `#0E2622`. The spec already lists "seat border on felt (1.13:1)" as a known weak spot, accepted because labels carry the information. | follows the spec | players confirm the known weak spot. Changing seat/felt colours changes frozen tokens → owner decision; every new pair must pass the contrast tests. |
| F5 who bet what | bet marker per seat (UI-R4), amounts in full (UI-R16), bet → pot motion (UI-R25) | markers exist; bet → pot motion does not | partly the same missing motion as F1; "more highlight" is a design change to the bet marker |

Reading across the five points: F1, F3 and F5 share one cause — the table changes state instantly and silently. Implementing the motion UI-R25 already asks for is the one piece of work that needs no new decision.

Wording note: F5 says "money". v1.x has play chips only and never says money (UI-R1); the feedback is about bet amounts.

## owner answers (2026-09-28)
| question | answer |
|---|---|
| reopen the frozen design, or change in code? | **open** — owner: "kind of both design and not design at the same time". Still in [[awaiting_owner_review]] with a recommendation. |
| chips (F1) | **keep UI-R4 / UI-R1 for now** — no chip visuals |
| music / sound (F2) | **sound effects and vibration, on by default**. Music was not chosen. → UI-R38, UI-R39 |
| what is too fast (F3) | **showdown, dealing, and bet / raise / call** — actions have no highlight and pass too quickly to follow who bet and who called → UI-R37. The 3s pause between hands was not named. |
| which version | **v1.0** |

## sources
- Owner, in chat, 2026-09-28, relaying testers' comments on the live site. Second-hand; no recordings or screenshots.
- Code facts: `../poker-monorepo` at `fda7539` — `packages/ui/src/styles/motion.css`, `packages/ui/src/components/Table.tsx`, `apps/web/src/screens/TableScreen.tsx`, `apps/server/src/config.ts`.

## related
[[ui_design_system]] · [[design_system_ground_rules]] · [[v1_0_casual_multiplayer_poker]] · [[ui_visual_foundations]] · [[awaiting_owner_review]]
