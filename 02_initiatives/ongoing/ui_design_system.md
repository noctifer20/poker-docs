---
type: initiative
status: active
priority: p0
milestone: v1.0
created: 2026-09-23
target: 
tags: [initiative]
---

# ui_design_system

## goal
The product has a deliberate, owner-approved visual identity and a design system that the v1.0 table UI is built from: tokens (colour, type, spacing, radius, motion), the v1.0 component set, and written ground rules that let v1.1 / v2.0 / v3.0 screens extend it without redesigning it.

## why
The owner considers the UI potentially the most important part of the product (2026-09-23). There was no visual direction and nothing in the vault or code about design; the v1.0 table UI task had nothing to be built from. The hardest layout problem (a 9-max table on a phone) must be solved before components are styled.

## scope
**in:**
- **process (owner, 2026-09-23):** research and direction exploration happen here (vault + agents); **Claude Design** (claude.ai/design) prototypes the chosen direction and holds the design system. Code in `../poker-monorepo` is built from it afterwards.
- research: poker-app UI conventions and competitors, phone-table layout patterns, card/chip legibility, typography, colour and accessibility → `05_research/`
- 2–3 written visual directions (principles, mood, palette intent, type pairing, references) for the owner to choose from
- a design brief that hands the chosen direction to Claude Design
- ground-rules spec in `04_specs/` with numbered requirements: what is fixed (core palette roles, type scale, table layout principle, accessibility floor) vs. what later versions may extend
- v1.0 component set: playing card, chip / chip stack, seat (incl. sitting-out, disconnected, acting + timer states), pot, community board, action bar (fold / check / call / bet / raise + bet sizing), table layout 2–9 seats on phone and desktop, blinds-level picker, nickname entry
- light and dark theme decision (both, or one deliberately)
- `design-reviewer` agent reviews every design output against the spec

**out:**
- v1.1 lobby/share screens, v2.0 wallet screens, v3.0 verification UI — the ground rules must leave room for them, but they are designed in their own versions
- logo / brand name (not decided; the system works with plain type until one exists)
- any money look in v1.x (currency symbols, coins, wallet imagery) and any fairness claim or badge before v3.0

## done when
- [x] research note(s) in `05_research/` with sources ✅ 2026-09-23
- [x] owner has picked a visual direction: **C · Modern Felt, sharpened**; four-colour deck on by default; dark only for v1.0 (owner, 2026-09-23) ✅ 2026-09-23
- [ ] Claude Design project holds tokens + v1.0 components + table prototype on phone (360px) and desktop; linked from this note
- [ ] `04_specs/design_system_ground_rules.md` exists with numbered ground rules, reviewed by `design-reviewer` with no open critical findings
- [ ] every text/background token pair meets WCAG AA (4.5:1 body, 3:1 large text and UI) in every shipped theme; suits and chip colours distinguishable under common colour-vision deficiencies
- [x] ADR on how the system lives in code accepted → [[0004_design_system_in_code]] ✅ 2026-09-24
- [ ] `packages/ui` built with every *known gap* fixed and the UI-R test suite green in CI

## tasks
- [x] research: poker UI competitors + phone-table layout patterns → [[poker_ui_competitors_and_table_layouts]] ✅ 2026-09-23
- [x] research: visual foundations — type, colour, card/chip legibility, accessibility → [[ui_visual_foundations]] ✅ 2026-09-23
- [x] write 2–3 visual directions from the research → [Table directions](https://claude.ai/artifact/SyXGGC8R5Xbpbbm33mtZxg) (A Home Game · B Instrument · C Modern Felt) ✅ 2026-09-23
- [x] owner picks a direction → C, sharpened; four-colour default; dark only for v1.0 ✅ 2026-09-23
- [ ] follow-up research (optional): screenshots of real 9-max phone layouts (PokerStars, GGPoker, WSOP) — no web source gave seat coordinates
- [x] ground rules + Claude Design brief drafted in [[design_system_ground_rules]] ✅ 2026-09-23
- [x] `design-reviewer` pass on the spec + brief (approve with changes), all findings applied → UI-R1–R28, 17 screens ✅ 2026-09-23
- [x] owner pastes the brief into Claude Design ✅ 2026-09-23
- [x] owner prototypes in Claude Design → **[Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy)** (private; tokens, 19 components, 17 screens, 3 signature options, self-hosted Barlow + OFL licence) ✅ 2026-09-23
- [x] owner picks the signature element → A · pointer tab ✅ 2026-09-23
- [x] `design-reviewer` pass on the Claude Design output (round 2: approve with changes; fix list in [[design_system_ground_rules]]) ✅ 2026-09-23
- [x] owner pastes the round-2 fix list into Claude Design; re-check (round 3): all 3 high + 7 medium verified ✅ 2026-09-23
- [x] round-3 fixes implemented in Claude Design (hole cards at large text, safe area, preset rounding/All-in merge, upward pot list, SeatSheet, pointer tab only) ✅ 2026-09-23
- [x] `design-reviewer` spot-check of round 3: 6/8 verified, no regressions ✅ 2026-09-24
- [x] design frozen (owner): Claude Design reference is enough; remaining gaps → spec *known gaps*, fixed in code; spec → `review` ✅ 2026-09-24
- [x] ADR 0004 drafted → [[0004_design_system_in_code]] (proposed) ✅ 2026-09-24
- [x] owner accepts [[0004_design_system_in_code]] ✅ 2026-09-24
- [x] engine: `legalActions(state)` query (to-call, min/max raise-to, raise reopened) — prerequisite for presets ✅ 2026-09-24 — `af50fb2`, see [[v1_0_casual_multiplayer_poker]]
- [x] `packages/ui`: tokens.json → generated tokens.css/tokens.ts; self-hosted Barlow + OFL ✅ 2026-09-24
- [x] `packages/ui`: port the 20 components to TSX + plain CSS, fixing every *known gap* in [[design_system_ground_rules]] ✅ 2026-09-24 — gap 10 partial (manual VoiceOver/TalkBack pass outstanding)
- [x] Ladle stories (one per Claude Design preview) + Playwright: contrast, geometry at 360×640 (100/150/200% text, with/without insets), axe-core, screenshots, banned words ✅ 2026-09-24
- [x] ADR 0004: design system in code — `packages/ui`, source of truth between Claude Design and the repo, DesignSync, visual regression tests ✅ 2026-09-24
- [ ] move `04_specs/design_system_ground_rules.md` draft → review once the Claude Design prototype confirms the rules hold at 360px

- [ ] manual screen-reader pass (VoiceOver iOS + TalkBack Android) over the table stories — known gap 10, can't be automated
- [ ] CI: no remote/CI yet — `packages/ui` tests only run locally; screenshot baselines are darwin-only (~18 MB of PNGs in git) — Linux CI needs its own set, consider Git LFS or CI-generated baselines
- [ ] portrait-only on phones: "rotate your phone" prompt in landscape orientation (owner, 2026-09-25 — no landscape phone layout in v1.0; UI-R17)

### playtest feedback, round 1 — [[playtest_feedback_2026_09_28]]
- [x] check why testers saw no dealing animation — F1 ✅ 2026-09-28: it fires, but only on the viewer's own two cards, ~185ms over 60px, in the same frame as blinds, markers and the action bar (measured on a local build of `fda7539`, not the live site)
- [ ] implement the UI-R25 motions missing in code: bet → pot, pot → winner, turn change, board reveal; with reduced-motion variants — F1, F5. No new decision needed. — **built on `feat/playtest-feedback-1`, not merged** (`4cd1852`)
- [ ] seat vs felt separation — F4. In v1.0 (owner, 2026-09-28). *How* (design round vs change in code) still open in [[awaiting_owner_review]] — **proposal built** (`3236337`, slate seats + brighter outline), waiting on owner
- [x] chip visuals — F1. Owner keeps UI-R4 / UI-R1 for now: no chip visuals ✅ 2026-09-28
- [ ] card size on the table — F1. In v1.0 (owner, 2026-09-28). **Proposal built** (`4360e5b`: hole cards 50×70 → 58×81, board 28×40 → 30×43, seat cards 26×36 → 28×39), waiting on owner
- [ ] highlight the acting seat and the amount on every bet / raise / call — F5, UI-R37 — **built on `feat/playtest-feedback-1`, not merged** (`47bc975`)
- [ ] sound effects + vibration, on by default, no music — F2, UI-R38/R39. First: verify browser support (iOS vibration, sound before first tap) and propose the event list to the owner — **built on `feat/playtest-feedback-1`, not merged** (`c683eca`); support verified: **no vibration on iPhone**, Android Chrome only; never listened to by a human
- [ ] pace dealing, bet / raise / call and showdown as distinct readable steps — F3, UI-R37. Durations found by trying values with testers — **built on `feat/playtest-feedback-1`, not merged** (`1d2fbbc`, `060f815`, `47bc975`); starting durations in `packages/protocol/src/pacing.ts`
- [ ] listen to the sounds and try vibration on a real Android phone; try the branch on a real iPhone — nothing was checked by ear or on a device
- [ ] manual screen-reader check of the new step captions (may be chatty)

## decisions
- [[0004_design_system_in_code]] (accepted 2026-09-24)
- design frozen at the Claude Design version of 2026-09-23 07:30 UTC (owner, 2026-09-24): no more design rounds; gaps fixed in implementation
- direction C · Modern Felt, sharpened; four-colour deck default-on; dark theme only for v1.0 (owner, 2026-09-23) — recorded in [[design_system_ground_rules]]
- process: Claude Design is the prototyping tool and home of the design system (owner, 2026-09-23) — a tool choice, not an ADR; the code-side ADR comes later
- [[0003_monorepo_structure_and_tech_stack]] — the web client stack the system must target (React + Vite)

## related
**Design system (Claude Design):** [Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy) · [[poker_ui_competitors_and_table_layouts]] · [[ui_visual_foundations]] · [Table directions](https://claude.ai/artifact/SyXGGC8R5Xbpbbm33mtZxg) · [[v1_0_casual_multiplayer_poker]] · [[roadmap]] · [[vision]] · `.claude/agents/design-reviewer.md`

## log
- 2026-09-23 — created. Owner: UI is potentially the most important part of the product; no visual direction exists yet; system must be expandable (v1.0 now, later versions extend it under ground rules). Process agreed: research + directions here, Claude Design prototypes and holds the system. `design-reviewer` agent added; two research tracks dispatched.
- 2026-09-23 — both research notes landed ([[poker_ui_competitors_and_table_layouts]], [[ui_visual_foundations]]; run as general-purpose agents following `researcher.md`, since vault agents don't load when Claude starts outside `poker-docs/`). Three directions published as a comparison page with the same 9-max phone moment in each, computed WCAG contrast and type specimens: A Home Game (light, paper/ink/cobalt), B Instrument (dark graphite, amber signal, mono numerals), C Modern Felt (flat green-teal felt, coral, semi-condensed). Ground rules common to all listed on the page (portrait, presets first, Fold spaced from Call, no colour-only states, tabular numerals, four-colour suits ≥5.4:1, reconnect countdown, no money look/fairness badges). Awaiting owner pick.
- 2026-09-23 — owner picked **C · Modern Felt, sharpened** (one signature element to be chosen from Claude Design's options), four-colour deck on by default, dark only for v1.0. Verified Barlow and Barlow Semi Condensed ship `tnum` (Google Fonts files). Drafted `04_specs/design_system_ground_rules.md`: ground rules UI-R1–R23 + a self-contained brief for Claude Design (13 screens, full component/state list). `design-reviewer` pass running.
- 2026-09-23 — `design-reviewer` verdict *approve with changes*: reference viewport was the phone screen (740) not the browser layout viewport (640); folded seats used opacity (muted text 3.3:1); danger indistinguishable from coral (ΔE 2.1 under tritanopia); ~12 rules-spec states missing from the brief (short all-in, BB option, split pot, uncalled bet, void hand, heads-up/dead button, leave mid-hand, top-up ≥100bb hidden…); presets undefined preflop; chip tiers conflicting with no-coins rule. All applied: spec renamed to [[design_system_ground_rules]] (name clash with this initiative), now UI-R1–R28; danger candidate #B8A1FF; bets = number + flat marker, no chip tiers in v1.0; 360×640 reference; brief grows to 17 screens. Leave-mid-hand wording corrected against R42 (it's a disconnect, not an instant fold).
- 2026-09-23 — owner built the system in Claude Design → [Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy): three-tier OKLCH tokens, full contrast table, suits re-searched for colour-blind separation (claims ΔE_OK ≥ 0.12 in all three simulations), violet danger, mint for others' turns/winners, React bundle (`window.Felt`, typed), all 17 brief screens + Table stress tests, three signature options (recommends A · pointer tab). `design-reviewer` pass running against [[design_system_ground_rules]].
- 2026-09-23 — `design-reviewer` round 2 on [Modern Felt](https://claude.ai/artifact/684t9mutghLZBVsCFB1ydy): *approve with changes*. Verified by local render + recomputation: all 76 README contrast pairs reproduce, suits ΔE_OK ≥ 12.5 in every simulation, 9-seat stress tests fit at 360×640 with no overlap, no banned words. High: 200% text doesn't scale (px type), timer hidden while the bet sizer is open, action buttons can show rounded amounts. Medium: pot-fraction formula wrong for re-raises, side pots overflow at 5+, banners cover top seats, losing hand shown at showdown (vs R10), sit-out countdown 4:32 (vs ~2 min), role=application + unmodified shortcuts, seat trays can collide. Spec tightened (UI-R16/21/23/24/27/28, new R29); fix list for Claude Design added. Reviewer backs signature A (pointer tab). New owner question: does R8 force an uncontested winner to show?
- 2026-09-23 — round-3 re-check (`design-reviewer`, headless Chrome render): all round-2 high/medium fixes verified, no regressions at 100%. New: action bar covers hero cards at ≥150% text; table ignores safe-area insets. Owner decided: signature **A · pointer tab**, **add** tap-a-seat detail sheet (WCAG 1.4.4), bar covering lower seats at 200% accepted. Spec now UI-R1–R34; round-3 fix list queued for Claude Design.
- 2026-09-24 — round-3 spot-check: hole cards in large-text bar, preset rounding/All-in merge, upward pot list, viewport meta, B/C removed — verified; no regressions. Open: SeatSheet hides the action bar on your turn and lacks focus/Escape; safe-area layout scrolls on a 360×640 screen with insets. UI-R17 reworded (scale to fit), UI-R35/R36 added; round-4 list queued. Reviewer: fine to start ADR 0004 in parallel.
- 2026-09-24 — owner froze the design: "what we have right now is enough"; no more Claude Design rounds. Round-4 list not sent; all open issues from reviews 2–4 recorded in [[design_system_ground_rules]] → *known gaps — fix in implementation* (13 items); spec → `review`. Drafted [[0004_design_system_in_code]]: repo canonical, port bundle to typed `packages/ui`, rules into an engine `legalActions` query, Ladle + Playwright tests enforce UI-R*.
- 2026-09-24 — owner accepted [[0004_design_system_in_code]]. Next: engine `legalActions`, then the `packages/ui` port.
- 2026-09-24 — engine `legalActions` landed (`af50fb2`). `packages/ui` port dispatched to a subagent on branch `feat/ui-package` in worktree `../poker-monorepo-ui` (isolated from engine work on `main`), from the frozen Modern Felt source downloaded from the artifact (version 1790202839-567c). Phased: tokens/fonts/format → components + known-gap fixes → Ladle stories → Playwright. In progress.
- 2026-09-24 — `packages/ui` (`@poker/ui`) ported by a subagent on `feat/ui-package` (5 commits `f00142e`…`b0c0f59`), merged into `main` as `2614979`. Tokens pipeline (generated files committed, staleness test), 20 components in TSX + CSS, 74 Ladle stories (Ladle 5.1.1 / React 18.3.1), Playwright 1.56.1 suite as a separate turbo `test:e2e`. **Verified myself:** forced `build/test/lint` green on the merged tree (ui 353 unit tests, engine 63, server 4) and `test:e2e` 577 passed (geometry 124, axe + copy on every story 148, screenshots 296, behaviour 9). All 13 known gaps addressed with tests; gap 10 partial. Source-vs-spec defects found and fixed: pot pills wrapping into top seats' cards at 150/200% text (round 3 had reported clean), "+N side pots" button 22px (< UI-R19 24px), `font:` shorthands resetting tabular figures (UI-R12). Deviations from ADR 0004 / `index.d.ts`: preset signatures take `{toCall, min, max}` (+`disabled`), callbacks added (`onAction` carries the displayed amount, etc.), bars/sheets outside the scaled drawing, desktop layout gained a centre seat (source crashed at 6/2 seats), `window.Felt` removed. Assumptions: open seats not tappable; sheets trap Tab but action-bar taps still work during your turn; the 600ms stale-tap window also applies at turn start. Open: landscape-phone layout, 320px-wide phones render the 11px floor at ≈9.4px (UI-R14 defined on the reference drawing), manual SR pass, CI.
- 2026-09-25 — `/owner-review`: owner confirmed the **16-character** nickname limit (UI-R15), chose **portrait-only** phones with a rotate prompt over a landscape layout (UI-R17), and **accepted** ≈9.4px glyphs on 320px-wide phones — UI-R14's floor is measured on the reference drawing. Spec updated; the rotate prompt replaces the landscape-layout task.
- 2026-09-28 — first player feedback on the live build recorded in [[playtest_feedback_2026_09_28]] (F1–F5: visuals/animation/card size, music, pace, green-on-green seats, bet readability). Mapped to spec and code: only a 200ms deal animation exists; the other UI-R25 motions are unimplemented. Tasks added; six of eight wait on the owner because they touch the frozen design or add scope.
- 2026-09-28 — owner answered four of five feedback questions: keep the no-chips rules for now; add sound effects + vibration, on by default (no music); too fast = showdown, dealing and bet/raise/call; all of it is part of v1.0. Spec gained UI-R37 (paced, followable table), UI-R38/R39 (sound & vibration). How to make the visual changes (design round vs code) still open.
- 2026-09-28 — subagent dispatched to build playtest feedback round 1 on branch `feat/playtest-feedback-1` (worktree `../poker-monorepo-feedback`, off `fda7539`; no merge, push or deploy). Part A: deal animation, UI-R25 motions, UI-R37 pacing/highlight, UI-R38/R39 sound + vibration. Part B, as separate *proposal* commits with before/after screenshots: seat vs felt colours, card size. Built under labelled assumptions for pacing durations, sound event list and an off switch. **Running — no result yet.**
- 2026-09-28 — subagent finished feedback round 1 on `feat/playtest-feedback-1` (worktree `../poker-monorepo-feedback`, 7 commits off `fda7539`; `main` and `origin/main` untouched, nothing deployed). **Part A** (5 commits): pacing from one shared table `packages/protocol/src/pacing.ts` — client plays each update as steps, server pushes the turn timer and next deal back by the same amount, no legal actions shown while steps play; all UI-R25 motions; action tag + bet outline; synthesised sounds (no audio files) and vibration with two off switches. **Part B** (2 proposal commits): slate seats with brighter outline; larger cards. Screenshots in the worktree under `proposals/playtest-feedback-1/`. Verified myself: forced `turbo run build test lint` 15/15, 0 cached (protocol 15, engine 88, server 97, ui 389, web 153); banned-word hits are code comments only; compared before/after screenshots — the seat change is subtle. Not re-run by me: the e2e suites (agent reports ui 691, web 7 passed). Not verified by anyone: sound by ear, vibration on a device, real phones, screen readers. Vibration cannot work on iPhone (caniuse.com/vibration, retrieved 2026-09-28). Spec correction: seat border on felt is 3.52:1 in code; the spec's 1.13:1 is from the starting palette.
- 2026-09-29 — `feat/playtest-feedback-1` (part A + part B proposals) merged into `dev` (`315852b`) and pushed; deploys to https://poker-dev.noctifer20.com for the owner to try on real phones ([[0006_dev_review_environment]]). Not in `main`.
