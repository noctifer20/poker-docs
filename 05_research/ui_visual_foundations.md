---
type: research
topic: visual foundations for a poker game UI (typography, colour, cards/chips, motion, touch, tokens)
source: web (WCAG/W3C, Apple HIG, Material, Google Fonts, foundry pages, design-system docs)
retrieved: 2026-09-23
created: 2026-09-23
tags: [research, design-system, ui, typography, colour, accessibility]
---

# ui_visual_foundations

## summary
Research for the [[ui_design_system]] initiative, feeding 2–3 visual directions for the owner to choose between. Covers six foundations for a mobile-first, dark-leaning, expandable design system for No-Limit Hold'em: type (candidates with tabular figures and small-size legibility), colour (WCAG 2.2 AA today, APCA/WCAG 3 not yet usable as a spec target, colour-vision-deficiency-safe suits/chips), card & chip legibility, motion (`prefers-reduced-motion`, timing norms), touch ergonomics (thumb zone, minimum target sizes), and design-token structure (W3C DTCG format now stable). All findings are from primary or near-primary sources unless flagged **unverified/from memory**.

## key points

### 1. Typography

**Requirements from the brief:** legible UI text at 11–14px on phones, tabular figures for chip counts/timers, open licence for web embedding, and legible rank glyphs at tiny card-corner sizes.

- **Tabular figures** (`font-variant-numeric: tabular-nums` or the font's `tnum` OpenType feature) make digits monospaced-width so stacked/changing numbers (pot size, timer, stack) don't jitter horizontally as digits change — this is a UI requirement, not just an aesthetic one, for any number that updates in place. (MDN `font-variant-numeric`; Google Fonts glossary on numerals/figures.)
- Google Fonts' own glossary distinguishes **tabular vs. proportional** and **lining vs. old-style** figures; UI numerals should be **tabular + lining** (all-same-height, all-same-width) for scoreboards/timers. (fonts.google.com/knowledge/glossary/numerals_figures — retrieved 2026-09-23)
- WCAG 2.2 AA text contrast (below) interacts with type choice: thinner/lighter weights need *more* size or contrast headroom at small sizes; this pushes toward fonts with a **high x-height** and open apertures, which read better at 11–14px on phone screens.

Candidate fonts (all Google Fonts / open licence, embeddable):

| font | licence | weights avail. | tabular figures | x-height / small-size notes | good for |
|---|---|---|---|---|---|
| **Inter** | SIL OFL 1.1 | Variable, 9 static weights | Yes — `tnum` + slashed-zero stylistic set | Designed explicitly for UI screens at small sizes; very high x-height, open apertures; author reports legibility at 13px in dense tables | UI text, numerals |
| **IBM Plex Sans** | SIL OFL 1.1 | 7 weights + italics, variable | Has tabular figure support (IBM Plex family designed for data-dense IBM products) | Slightly more "designed" character than Inter but stays clear at small sizes | UI text, brand character |
| **IBM Plex Mono** | SIL OFL 1.1 | 7 weights | Monospace — all figures inherently tabular | Distinct letterforms (dotted zero variants exist in family), good for hard financial-style displays without looking like a bank ticker | Timers, chip counts as a deliberate "readout" style |
| **Roboto** | Apache 2.0 (not OFL, but open) | Full family, variable (Roboto Flex) | Yes, tabular figures supported | Google's UI workhorse; well-hinted at small sizes; huge language coverage | UI text baseline; safe default |
| **Source Sans 3** | SIL OFL 1.1 | 6 weights + italics, variable | Yes | Adobe's open UI sans; clean, wide weight range, reads well small | UI text alternative to Inter |
| **Space Grotesk** | SIL OFL 1.1 | 5 weights (Light–Bold) | Yes — old-style *and* tabular figures, plus fractions/superscript-subscript | Geometric, slightly more character/display feel; proportional sibling of Space Mono | Headlines, branding accents, NOT recommended for dense 11px body text (more distinctive = more idiosyncratic at tiny sizes) |
| **Atkinson Hyperlegible** | SIL OFL 1.1 | Regular/Italic/Bold/BoldItalic (also "Next" variable at Adobe Fonts) | Not confirmed as a stated feature (unverified) | Purpose-built by the Braille Institute to maximize letterform *distinction* (e.g. easily confused glyphs like 1/l/I, 0/O) for low-vision readers — directly relevant to a game where mis-reading a digit costs chips | Accessibility-mode / high-legibility theme candidate |
| **JetBrains Mono** | SIL OFL 1.1 | 8 weights, variable | Monospace — inherently tabular | Built for code; very open counters, disambiguated 0/O and 1/l/I by design | Alternate "readout" mono for timers/stacks if IBM Plex Mono's character is unwanted |

**Card-face typography (rank glyphs at tiny sizes):** no rigorous public research was found on *digital* card corner-index typography specifically (searches returned only physical-deck retailer copy, not type-design research) — flag as a gap. What is documented, from physical card design: the corner **index** must work at a glance and across-table distance; **jumbo index** decks exist because standard corner numerals (~1/2") are hard to read at range, and enlarging to ~1" solves it (dkgameroomoutlet.com, ezracard.com — retailer sources, not type research, treat as **anecdotal/unverified** but consistent with plain legibility logic). For a phone UI, the practical translation: rank glyphs (A K Q J T 9…) at the size they'll actually render (often under 16px on a 9-max mobile layout) need a **bold, high-x-height numeral/letter set** — the same tabular/lining figures used for chip counts are a reasonable base, possibly at a heavier weight than body text, rather than a separate decorative "card font."

**Pairing suggestions:**
- **Inter (UI text + tabular numerals) + IBM Plex Mono (timer/pot/stack readouts)** — clean UI baseline with a deliberately distinct "instrument" face for numbers that must never be misread.
- **Source Sans 3 (UI text) + Space Grotesk (headlines/branding, sparingly)** — if the direction wants more visual personality than Inter's neutrality, confined to non-critical text.

### 2. Colour

**Contrast — what's actually enforceable today:**
- WCAG 2.2 SC 1.4.3 (AA): normal text ≥ **4.5:1**; "large text" (≥18pt / ≥14pt bold, ≈24px / ≈18.5px) ≥ **3:1**. (w3.org/WAI/WCAG22/Understanding/contrast-minimum.html, retrieved 2026-09-23)
- WCAG 2.2 SC 1.4.11 (AA, Non-text Contrast): UI components and states (borders of inputs, icons that convey meaning, focus indicators) need ≥ **3:1** against adjacent colour. (w3.org/WAI/WCAG22/Understanding/non-text-contrast.html)
- **APCA / WCAG 3**: APCA (the perceptual contrast algorithm behind the "Lc" numbers some tools show) was explored for WCAG 3 but **is not part of the current WCAG 3 working draft** — it was marked exploratory and effectively dropped from that track; WCAG 3 itself is a Working Draft not expected to reach Recommendation until roughly 2028–2030, and WCAG 2.2 AA remains the operative legal/contractual bar today (adrianroselli.com/2026/04/wcag3-contrast-as-of-april-2026.html; github.com/w3c/wcag3 issue #29; accessibe.com WCAG 3.0 explainer — retrieved 2026-09-23). **Implication: target WCAG 2.2 AA numbers as the spec requirement; APCA can inform *design judgement* (it's already the contrast model Radix Colors uses internally) but must not be cited as a compliance claim.**

**Colour-vision deficiency (CVD) and suits:**
- Red-green CVD affects roughly 8% of men and 0.5% of women (commonly cited figure across CVD literature, repeated in multiple secondary sources retrieved 2026-09-23 — **treat the exact percentage as a commonly-cited approximation, not independently verified against a primary clinical source in this pass**). Standard red/black suit colouring is a known failure case for these players when colour is the *only* signal.
- **Four-colour decks** are an established real-world accessibility/legibility aid: commonly hearts=red, spades=black, clubs=green, diamonds=blue (or yellow, in some variants), specifically to widen the perceptual gap beyond red-vs-black. This is a direct, low-risk pattern to adopt or offer as a toggle. (Wikipedia "Four-color deck"; pokercine.com — retrieved 2026-09-23, secondary sources, but the pattern is long-established real-world convention, not a novel claim.)
- **Design rule this implies:** never encode suit identity by colour alone — pair colour with the suit *glyph shape* (♠♥♦♣ are already shape-distinct, which helps) and consider a four-colour-deck theme option, not just red/black, as a first-class palette variant rather than an afterthought.
- Same logic applies to **chip denomination colour** and **"your turn"/danger state** colour: never the only signal. Use shape/position/motion/icon redundancy (e.g. an acting seat gets a ring + timer arc + label, not just a colour highlight; a danger/all-in state gets an icon or pattern, not just red).

**Palette-building approaches:**
- **OKLCH-based scales**: OKLCH (cylindrical form of Oklab, Björn Ottosson, 2020) is now the practical basis for generating perceptually-uniform tonal ramps (the 50→950-style scales used by Tailwind, Radix, Material) — hold hue (H) and chroma (C) roughly constant, vary lightness (L) only, to get a scale where equal *L* steps look like equal *perceived* lightness steps, unlike sRGB/HSL ramps. This matters for a dark, content-dense table UI because it makes it possible to guarantee consistent contrast behaviour across a whole scale rather than tuning each shade by eye. (colorarchive.org OKLCH guide; robinrendle.com "Design systems, color spaces, and CSS" — retrieved 2026-09-23)
- **Radix Colors**: ships pre-built, accessibility-tuned 12-step scales (light/dark pairs, each step with a defined *role* — app background, subtle background, UI element background, hover, border, solid, solid-hover, text, high-contrast text, etc.) and explicitly designs its contrast targets around the APCA model, with a one-line light↔dark theme switch. This is a strong "borrow the token *structure*, not necessarily the exact hues" candidate for a poker table where roles (felt background, seat background, chip well, text, border, focus) repeat across many components. (radix-ui.com/colors — retrieved 2026-09-23)
- **Material 3 colour roles**: role-based tokens (primary / on-primary / primary-container / on-primary-container, secondary, tertiary, surface, on-surface, error, etc.) generated from a small set of seed colours via an algorithm (HCT colour space). Good reference for the *semantic role* vocabulary even if the visual style (Google's) isn't what we want. (m3.material.io/styles/color/roles — retrieved 2026-09-23)
- **Recommendation for this project**: build primitive scales in OKLCH (for perceptual-uniformity + easy dark-mode derivation), organise semantic roles Radix-style (background/surface/border/text tiers, each with clear states), and keep a small, poker-specific *game-state* role layer on top: `felt`, `seat-empty`, `seat-active`/turn, `seat-folded`, `pot`, `chip-{denomination-or-generic}`, `danger`/all-in, `win-highlight`.

**Dark vs light for a game table:** casino/game-app convention leans heavily dark by default — cited practical reasons are reduced eye strain in low light, better perceived contrast for chips/cards against a dark felt-like surface, and battery savings on OLED phones; "classic" table themes use dark backgrounds (charcoal/near-black or deep green) with the *cards and chips* carrying the brightness and colour, rather than a bright UI chrome competing with the game elements. (blog.logrocket.com dark-mode UI practices; wizzydigital.org "Are Dark and Light Modes Essential for Online Casino UI"; moxietalk.com — retrieved 2026-09-23, general design-commentary sources, not empirical studies — **treat as informed industry convention, not measured research**.) Given the brief explicitly forbids money/casino *vocabulary and look* in v1.x, this argues for a **dark-first, non-felt-green** direction — dark for the ergonomic/legibility reasons above, but the palette itself should avoid the literal "green felt + gold trim" casino signifier so it doesn't read as a casino/gambling product.

### 3. Card & chip design

- **Corner index legibility**: the physical-card convention (rank+suit in the corner, readable while cards are fanned/overlapped, i.e. without seeing the full face) is the direct precedent for a compact mobile hand display. Jumbo-index decks exist because standard indices become illegible at distance/small size — the fix used there (enlarge, bold, high-contrast) is the same lever available in a UI: don't shrink card corner glyphs below the point where rank and suit are independently recognizable, even under time pressure. No primary type-legibility research was found specific to *digital* card rendering; this section is inference from physical-card convention plus general small-size type legibility principles (x-height, stroke contrast), not a cited study — **flagged as an open question below**.
- **Suit shape redundancy**: ♠ ♣ are naturally darker/denser shapes, ♥ ♦ lighter — shape alone (not just colour) should remain the primary signal, with colour as reinforcement (see CVD section above).
- **Chip-as-token, not currency**: the brief bans money vocabulary/look in v1.x. Real casino chips communicate *value* through colour-coded denominations (white/red/green/black/purple/yellow ladder, roughly $1/$5/$25/$100/$500/$1000 — professionalrakeback.com, pokerology.com, retrieved 2026-09-23) plus printed numerals and edge-spot patterns. To read as a **game token rather than currency**, the v1.x system should keep the *ergonomic* pattern (stacked circular tokens, colour-coded by relative size tier, numeral for exact count) while dropping currency-coded signifiers: no `$`/coin iconography, no denomination values that map to real money (use round "chip units" or point-like values), no bill/wallet imagery anywhere near chips. This is a design-rule inference from the brief's constraint, not a sourced claim.
- Chip stacks as a UI element benefit from the same tabular-numeral treatment as timers (stack count must not jitter/reflow as it changes).

### 4. Motion

- **`prefers-reduced-motion`** is a CSS media feature reflecting an OS-level accessibility setting (`reduce` / `no-preference`); when `reduce` is set, large-scale motion (parallax, sliding panels, zooming) should be cut or replaced — a common, sound pattern is to **keep opacity fades, drop transforms** (slides/scales/parallax) so state changes are still perceivable without the vestibular trigger. (MDN-adjacent guidance summarized via openreplay.com/motionspec.dev, retrieved 2026-09-23 — implementation-blog sources, not a W3C spec page for this specific pattern, but the underlying media feature itself is a standard CSS feature.) **For this project this is a MUST, not a nice-to-have**: card deals, chip-slide animations and timer sweeps are exactly the kind of large-scale repeated motion that should degrade gracefully under `prefers-reduced-motion`.
- **Timing norms**: commonly cited UI-animation thresholds are **under ~200ms reads as instant**, **300ms+ starts to feel sluggish** for discrete UI transitions (button state, small element motion) — general UI-motion guidance, not poker-specific (72technologies.com, appypie.com — retrieved 2026-09-23, industry-blog sources, **treat as convention not measured law**). A **photosensitive-safety floor** applies regardless of style: don't flash/change state at ≥3 times per second (general WCAG-adjacent seizure-safety guidance).
- **Material Design motion** gives usable *vocabulary* even though we won't copy Google's visual style: standard/deceleration/acceleration/sharp easing curves, and duration scaled to the *distance travelled* by the moving element rather than one fixed duration for everything (i.e. a chip sliding across a 9-max table should take longer than a small icon state change). Material's 2025 shift to a physics-based "motion physics" model (M3 Expressive, replacing fixed easing/duration curves) is noted as a **direction**, not something to adopt wholesale — a game-state UI (deals, chip moves, timer) likely wants *predictable, repeatable* timing (easing-curve based) more than expressive physics, since players will watch these animations hundreds of times per session and consistency aids fast reading of game state. (m3.material.io/styles/motion — retrieved 2026-09-23)
- **Game-specific implication**: deal animation and chip-move animation are the two motions players will see most; both should be fast, consistent, and skippable/near-instant under reduced motion, since they're *informational* (communicating "the bet moved," "your card arrived") not decorative. The 30-second turn timer is the one motion that must remain legible even under `prefers-reduced-motion` — a numeric countdown plus a static/low-motion progress indicator (not a smooth animated sweep) satisfies both.

### 5. Touch ergonomics

- **Thumb zone**: Steven Hoober's 2013 observational study (>1,300 people) found **~49% one-handed grip, ~36% cradled + other-hand finger, ~15% two-thumb** phone-holding postures, and roughly **75% of touches are thumb-driven** (Smashing Magazine's summary of this and related research, retrieved 2026-09-23 — the original Hoober study itself is a 2013 UXmatters piece; this pass relied on secondary summaries, flagged as **secondary-source, not the primary 2013 article**). The resulting "thumb zone map" convention: **green** (bottom-center, easy reach) for primary actions, **yellow** (mid-sides, stretch) for secondary, **red** (top corners) for rarely-tapped/destructive-adjacent or purely informational content.
- **Direct implication for a 9-max poker table on a phone**: the action bar (fold/check/call/bet/raise + sizing) is the single most-tapped control set in the product and belongs in the **green zone** (bottom of the viewport); seat/opponent info, pot display and community cards — mostly *read*, not tapped — can live in the yellow/red zones. This is consistent with how the initiative's own scope already frames the action bar as bottom-anchored, and the research here gives it a cited rationale.
- **Minimum target sizes** (three overlapping standards, use the largest applicable):
  - **WCAG 2.2 SC 2.5.8 (AA)**: pointer targets ≥ **24×24 CSS px**, or ≥24px of clear spacing from adjacent targets if smaller (exceptions for inline text links, essential/legally-required sizing, user-agent-controlled controls). This is a **floor**, not a comfortable target. (testparty.ai/wcag-target-size-guide, allaccessible.org, dock.codes — retrieved 2026-09-23, secondary summaries of the SC; underlying SC is W3C WCAG 2.2.)
  - **Apple HIG**: recommends **44×44 pt** minimum touch targets.
  - **Material Design**: recommends **48×48 dp** minimum touch targets.
  - Rationale cited across these sources: average fingertip contact area is roughly 10mm, and users with tremor/limited dexterity can miss by 20–30px, so the *comfortable* target is meaningfully larger than the *legal minimum*. **Design rule: treat 24px as the absolute accessibility floor for spacing-compensated targets, but set the actual bet-sizing/action-button targets at 44–48px minimum given this is a fast, repeated-tap, sometimes one-handed game interaction.**

### 6. Design-token practice

- **W3C Design Tokens Community Group (DTCG) format**: shipped its **first stable specification, version 2025.10, on 2025-10-28**, backed by 40+ organisations (Adobe, Figma, Google, Microsoft, Shopify, Salesforce among them). Core shape: a token has a `$value` and a `$type`, and tokens can reference other tokens by path (aliasing). Tooling support is now broad: Figma, Penpot, Sketch, Tokens Studio, Style Dictionary and Terrazzo all read/write this shape. (w3.org/community/design-tokens 2025-10-28 announcement; designtokens.org/tr/drafts/format — retrieved 2026-09-23.) **This is the format to target** so the token set can move between Claude Design and the eventual `packages/ui` in the monorepo without a bespoke export step — directly relevant to the initiative's open ADR question about "source of truth between Claude Design and the repo."
- **Three-tier structure** (near-universal convention, not a single canonical source but consistent across design-system literature reviewed):
  1. **Primitive/global tokens** — raw values, no meaning attached (`blue-500`, `space-16`, `font-weight-600`).
  2. **Semantic tokens** — meaning + usage guidance, reference primitives (`color-action-primary`, `color-danger`, `space-stack-lg`). This is the layer that should carry poker-specific roles: `color-felt-surface`, `color-seat-acting`, `color-chip-tier-1..n`, `color-danger-allin`.
  3. **Component tokens** — specific to one component, reference semantic tokens (`button-bg-hover`, `card-corner-radius`, `chip-stack-shadow`).
  - The semantic layer is the thing that makes the system **expandable across versions without redesign**: v1.1 (private lobbies), v2.0 (wallet) and v3.0 (verification UI) can each introduce new *semantic* roles (`color-wallet-balance`, `color-verify-success`) that draw from the *same primitive scales* already established in v1.0, rather than inventing new palettes. This is the direct mechanism for the initiative's "later versions extend it without redesigning it" goal.

## implications for our design system

Worded as MUST/SHOULD candidates for the eventual `04_specs/design_system_ground_rules.md` — these are **proposals for the spec-writer/owner to formalise**, not accepted requirements:

- **MUST** meet WCAG 2.2 AA contrast for every shipped theme: 4.5:1 body text, 3:1 large text, 3:1 non-text UI components/states (already in the initiative's "done when," this research grounds the exact numbers and source).
- **MUST NOT** rely on colour alone to convey suit identity, chip denomination tier, "your turn," or danger/all-in state — pair every colour signal with shape, icon, position, or text redundancy.
- **SHOULD** offer a four-colour-deck theme (or default to one) rather than red/black-only, given red-green CVD prevalence and the established real-world precedent.
- **MUST** cite WCAG 2.2 AA (not APCA) as the compliance target; APCA/WCAG 3 may inform design judgement but must not appear as a compliance claim until WCAG 3 reaches Recommendation status.
- **MUST** use tabular (lining) numerals for every number that updates in place — pot, stack, timer, bet sizing — to prevent layout jitter.
- **SHOULD** adopt Inter (or Source Sans 3) as the UI-text primitive and a monospace face (IBM Plex Mono or JetBrains Mono) as the dedicated numeric "readout" face for timers/stacks/pot, to make numbers visually distinct from prose and harder to misread under time pressure.
- **MUST** build colour primitives as OKLCH-derived scales (perceptual-uniformity across light/dark), with a Radix-style semantic layer (background/surface/border/text roles with defined states) plus a poker-specific game-state role layer on top.
- **SHOULD** default to a dark theme; the palette should avoid literal "green felt + gold" casino signifiers to stay consistent with the no-money-look rule in v1.x, while still using dark-surface ergonomics (contrast, eye strain, chip/card pop) that the rest of the game industry converges on.
- **MUST** place the action bar (fold/check/call/bet/raise + sizing) in the thumb's "green zone" (bottom of a one-handed phone grip); primarily-informational elements (pot, board, opponent seats) may sit in yellow/red zones.
- **MUST** size interactive targets at minimum 24×24 CSS px (WCAG 2.5.8 floor with spacing) and **SHOULD** target 44–48px for primary action-bar controls (Apple HIG / Material comfortable minimums), given this is a fast, repeated-tap game.
- **MUST** respect `prefers-reduced-motion`: keep opacity-based state feedback, remove transform-based motion (deal slides, chip-move parallax, animated timer sweep) when set; the 30s turn timer must remain legible as a static/numeric element even with motion reduced.
- **SHOULD** author tokens in the W3C DTCG format (`$value`/`$type`, aliasing) in three tiers (primitive → semantic → component) from the start, so later versions add semantic roles onto existing primitives instead of new palettes, and so the token set can move between Claude Design and `packages/ui` without a bespoke translation step (feeds the initiative's still-open "design system in code" ADR).
- **SHOULD** treat chip design as token-first, not currency-first: colour-tiered stacked circular tokens with numeral labels, no `$`/coin/wallet iconography, no denomination that maps to a real-money value, consistent with the v1.x "no money look" scope boundary.

## open questions
- No primary type-design research was found specifically on **digital card corner-index legibility at phone sizes** (vs. physical playing-card convention) — this pass's card-typography guidance is inference, not a cited study. Worth a targeted follow-up search (type-design/accessibility literature, or testing our own candidate sizes) before locking the spec's minimum card-glyph size.
- The **~8% male / 0.5% female red-green CVD prevalence** figure is widely repeated but wasn't traced to a single primary clinical/epidemiological source in this pass — fine for a design rationale, not sturdy enough to cite as a hard statistic in a spec without a better source.
- **APCA's status is actively moving** (explicitly dropped from the current WCAG 3 draft, but under continued research per the w3c/wcag3 GitHub issue tracker) — re-check before final spec sign-off in case it re-enters the draft or a stable APCA-based guideline emerges outside WCAG 3.
- Dark-mode-for-casino-UI claims here are industry-blog consensus, not measured studies (e.g. no eye-tracking or usability study was found comparing dark vs light poker table comprehension speed) — if the owner wants stronger evidence before committing to dark-first, that would need a dedicated study search or our own light-vs-dark prototype test.
- Whether to default to a **four-colour deck** or keep it as an optional/settings toggle is a product decision, not just an accessibility one (traditionalists expect red/black); flagging for the owner rather than deciding here.
- Material's 2025 shift to physics-based motion ("M3 Expressive") is new enough that its practical trade-offs for a *fast, high-repetition* game UI (vs. fixed-curve predictability) aren't yet well documented outside Google's own materials — worth revisiting once more third-party critique exists.

## what would change these conclusions
- If WCAG 3 reaches a later-stage draft that reinstates APCA with concrete pass/fail thresholds, the contrast MUST-rule should be revisited (likely additive, not a replacement, given the years-long parallel-support transition period called out by W3C-adjacent commentary).
- If user testing (once there's a prototype) shows chip-stack or timer numerals are misread at the actual rendered mobile size, the font/weight choice (and possibly the "tabular figures suffice" assumption) should be revisited before locking the type scale in the spec.
- If the owner decides four-colour decks feel "not poker enough," the suit-colour MUST/SHOULD above should be renegotiated down to a settings toggle rather than a system default.
- New primary research specifically on digital playing-card legibility (if found) should replace the current physical-deck-convention inference in section 3.

## sources
- W3C, "Understanding Success Criterion 1.4.3: Contrast (Minimum)" — https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html (retrieved 2026-09-23)
- W3C, "Understanding Success Criterion 1.4.11: Non-text Contrast" — https://www.w3.org/WAI/WCAG22/Understanding/non-text-contrast.html (retrieved 2026-09-23)
- W3C WCAG 2.2 Recommendation — https://www.w3.org/TR/WCAG22/ (retrieved 2026-09-23)
- W3C WCAG 3.0 Working Draft — https://www.w3.org/TR/wcag-3.0/ (retrieved 2026-09-23)
- Adrian Roselli, "WCAG3 Contrast as of April 2026" — https://adrianroselli.com/2026/04/wcag3-contrast-as-of-april-2026.html (retrieved 2026-09-23)
- W3C wcag3 GitHub, Issue #29 "Contrast Research: APCA Peer Reviews..." — https://github.com/w3c/wcag3/issues/29 (retrieved 2026-09-23)
- TestParty, "What Is the WCAG 2.5.8 Target Size Minimum" — https://testparty.ai/blog/wcag-target-size-guide (retrieved 2026-09-23)
- Design Tokens Community Group, "Design Tokens specification reaches first stable version" — https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/ (retrieved 2026-09-23)
- Design Tokens Format Module 2025.10 — https://www.designtokens.org/tr/drafts/format/ (retrieved 2026-09-23)
- Radix Colors — https://www.radix-ui.com/colors (retrieved 2026-09-23)
- Material Design 3, "Color roles" — https://m3.material.io/styles/color/roles (retrieved 2026-09-23)
- Material Design 3, "Motion" — https://m3.material.io/styles/motion/overview/how-it-works (retrieved 2026-09-23)
- colorarchive.org, "OKLCH Color Space: The Developer's Guide" — https://colorarchive.org/guides/oklch-color-space-guide/ (retrieved 2026-09-23)
- Robin Rendle, "Design systems, color spaces, and CSS" — https://robinrendle.com/notes/design-systems-color-spaces-and-css/ (retrieved 2026-09-23)
- Wikipedia, "Four-color deck" — https://en.wikipedia.org/wiki/Four-color_deck (retrieved 2026-09-23, secondary)
- Google Fonts Knowledge, "Numerals, or figures" — https://fonts.google.com/knowledge/glossary/numerals_figures (retrieved 2026-09-23)
- Inter typeface — https://fonts.google.com/specimen/Inter and en.wikipedia.org/wiki/Inter_(typeface) (retrieved 2026-09-23)
- IBM Plex — https://github.com/IBM/plex, https://fonts.google.com/specimen/IBM+Plex+Mono (retrieved 2026-09-23)
- Atkinson Hyperlegible — https://github.com/googlefonts/atkinson-hyperlegible, SIL OFL license page via Font Squirrel (retrieved 2026-09-23)
- Space Grotesk — https://github.com/floriankarsten/space-grotesk, https://fonts.google.com/specimen/Space+Grotesk (retrieved 2026-09-23)
- JetBrains Mono OFL license — https://github.com/JetBrains/JetBrainsMono/blob/master/OFL.txt (retrieved 2026-09-23)
- Smashing Magazine, "The Thumb Zone: Designing For Mobile Users" (summarizing Steven Hoober's 2013 research) — https://www.smashingmagazine.com/2016/09/the-thumb-zone-designing-for-mobile-users/ (retrieved 2026-09-23, secondary source)
- MDN, `font-variant-numeric` — https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/font-variant-numeric (retrieved 2026-09-23)
- Poker chip colour/denomination convention — https://professionalrakeback.com/casino-chips, https://www.pokerology.com/poker/rules/chips-value/ (retrieved 2026-09-23, secondary/industry sources)
- Dark-mode casino UI convention — https://blog.logrocket.com/ux-design/dark-mode-ui-design-best-practices-and-examples/, https://wizzydigital.org/are-dark-and-light-modes-essential-for-your-online-casino-ui/ (retrieved 2026-09-23, industry-commentary sources)
- Jumbo vs. standard card index — https://www.dkgameroomoutlet.com/blog/2012/10/19/playing-cards-jumbo-index-vs-regular-or-standard-index/, https://ezracard.com/playing-card-size/ (retrieved 2026-09-23, retailer sources, anecdotal)

## related
[[ui_design_system]] · [[roadmap]] · [[vision]] · [[glossary]]
