---
type: research
topic: poker UI competitors and phone-table layout patterns
source: web (see sources)
retrieved: 2026-09-23
created: 2026-09-23
tags: [research, ui, design-system, competitors]
---

# poker UI competitors and phone-table layout patterns

## summary
Surveyed how major online-poker rooms (PokerStars, GGPoker, partypoker, 888poker), social apps (Zynga Poker, WSOP), club apps (PokerBros/ClubGG/PPPoker), a home-game app (Poker Now, EasyPoker), and a couple of redesign case studies lay out a table on a phone, handle bet sizing, and treat state (sit-out/disconnected/timer). Primary sources are thin on pixel-level detail — operators don't publish spec sheets — so most findings come from industry news (Pokerfuse, PokerIndustryPRO, PokerNews), operator help docs/blogs, app store listings/reviews, and a few designer case studies. Several claims below are secondary-source paraphrase, not verified against the live apps; this is flagged per item. No source gave hard seat-position coordinates for 6-max/9-max on phone — that remains a gap (see open questions).

## key points

### 1. Table layout on phone: portrait is now the default across the industry
- **The whole online-poker industry shifted from landscape-only to portrait-first mobile clients starting ~2019-2020.** partypoker's Nov 2019 mobile relaunch was one of the first, explicitly redesigning around a vertical table instead of rotating the desktop's horizontal layout [F5 Poker, 2019; Pokerfuse, 2019]. PokerNews' March 2020 roundup frames this as an industry-wide move away from the "horizontal rectangular layout... thought for a desktop environment" [PokerNews, "Latest App Updates Show Big Bets on Vertical Poker Clients", Mar 2020].
- **888poker's Android overhaul** (undated, referenced 2023-2024 coverage) replaced its landscape/side-swipe lobby with a portrait grid and added a redesigned portrait table plus up to 4-table multi-tabling [Pokerfuse; PokerIndustryPRO].
- **PokerStars** supports both orientations and can switch between them; the operator invested in "extensive research and prototyping... to define the most ideal solution for landscape and portrait modes of the poker lobby, multi-level game filtering and poker table" per a design agency's portfolio case study, though that page gives no layout specifics [Lackabane Consultancy case study, undated — **secondary, marketing framing, low detail**].
- **GGPoker**: portrait table is described as letting a player hold the phone in one hand while the other hand is free; landscape is recommended when you want "the full view" and more on-screen info, portrait is positioned as adequate for single-tabling [PokerNews 2020; secondary blog aggregation, 2026 — unverified against the live app].
- **PokerBros/ClubGG**: "vertical mobile layout" as primary; ClubGG's client (riding on GGPoker's backend) is described as closely mirroring GGPoker's mobile look [bluffingmonkeys.com comparison articles, 2025/2026 — **secondary, affiliate/review-site tone, treat cautiously**].

**Unverified/from-memory-only (not found in sources above, flagged per project rules):** the typical mobile portrait pattern across these rooms is roughly: opponent seats arranged in an arc around the top ~60% of the screen, community cards + pot centered mid-screen, the player's own two hole cards and stack anchored bottom-center just above the action bar, and the action bar (fold/check-call/bet-raise + sizing controls) pinned to the bottom safe area. This matches general mobile-game "thumb zone" guidance found in secondary sources (see UX complaints section) but no primary source was found that draws this out explicitly for 6-max vs. 9-max seat placement. **This is our biggest layout-detail gap — see open questions.**

### 2. Bet-sizing controls: presets + slider is the norm; manual entry is the precision fallback
- **GGPoker** offers three bet-sizing input modes side by side: preset buttons (1/3 pot, 1/2 pot, 3/4 pot, pot), a slider for custom sizing, and manual numeric entry. Guidance explicitly warns the slider "requires careful finger placement" and recommends presets/manual entry to avoid misclicks, especially in tournaments where exact sizing matters [ggpoker.com blog, "All About the GGPoker Mobile App", undated; corroborated by secondary aggregation].
- GGPoker also ships **"Smart Betting"**, an AI feature suggesting personalized bet sizes for mobile to reduce slider/typing friction and support one-handed play [PokerNews, "Use Smart Betting to Improve Your Game on GGPoker", May 2020; pokernewsdaily.com].
- **partypoker**: bet slider controlled by an upward swipe gesture, paired with preset betting amounts and "clear action buttons" [PokerNews, Mar 2020].
- **Recommendation surfacing in multiple secondary sources**: prefer preset buttons over a bare slider as the primary sizing method precisely because sliders are the most misclick-prone control on a touchscreen at speed.

### 3. Club apps (PokerBros / ClubGG / PPPoker) — same core UI family, different backend model
- All three run on a **private-club model**: games live inside invite-only clubs/unions rather than a shared public pool — the opposite of what v1.0's "public 9-max tables" needs, but directly relevant to v1.1 (private lobbies) [cc-poker.com, bluffingmonkeys.com, 2025/2026 comparison articles — **secondary, affiliate-style sources**].
- **ClubGG**: "clean and intuitive interface," vertical mobile layout, closely mirrors GGPoker's mobile client (same network); adjustable per-hand timer (13-25s) plus a time bank.
- **PokerBros**: customizable avatars/table themes/colors; leans social (rapid chat, animated emojis); occasional ad/event pop-ups noted as a mild negative.
- **PokerBros App Store reviews** (primary source: live review page) are dominated by RNG/fairness complaints (perceived rigged run-outs, improbable win rates), not UI complaints — reviewers who liked it called it "looks good, plays good." **No UI-specific complaints surfaced in the reviews sampled**, which is itself a data point: for this app, RNG trust is the dominant player pain point, not layout. [App Store, PokerBros reviews page, retrieved 2026-09-23].

### 4. Social / play-money apps
- **Zynga Poker**: offers 5-player and 9-player table sizes, themed decks/avatars/table designs. **App Store reviews (primary)** show two recurring complaints relevant to us: (a) **timing-sensitive misclicks** — a reviewer describes tapping "check" just as another player raises, and the app registering it as a call instead of un-checking the box, i.e. a race condition between a queued action and a table-state change; (b) **disconnect handling** — "you sometimes get bumped from a table and have to rejoin... your hand has been folded," an abrupt, punishing disconnect/reconnect experience with no visible grace period. Both are directly relevant to our sit-out/disconnected-state and action-queuing design. [App Store, Zynga Poker reviews, retrieved 2026-09-23]
- **WSOP (social app)**: 9-max tables are standard; found no primary UI documentation (WSOP's own mobile-app FAQ page returned HTTP 403 to automated fetch, so it could not be reviewed for this note — treat WSOP findings as **not verified**, gap noted below).

### 5. Home-game / friends apps (relevant to v1.1) — a genuinely different UI paradigm
This is the most useful finding for how v1.1 (and arguably even v1.0) could differ from "online room" conventions:
- **Poker Now** (pokernow.com / poker-now apps): runs entirely in-browser, no install; a room link is enough to join from phone, tablet or desktop; built-in video/voice for up to 10 players so a remote group feels like a real home game — no third-party video app needed [poker-now marketing site, undated — vendor source, self-described].
- **EasyPoker** takes the most distinctive approach found in this research: it explicitly **rejects the standard top-down virtual-table view**. Its stated design philosophy: hold the phone **vertically like a physical hand of cards**, tap-and-hold to peek at your hole cards, and the interface deliberately drops "weird avatars, a wheel of fortune or bells and whistles" that live poker doesn't have. Its own framing: "a simple and beautiful digital version of your physical poker set" that removes shuffling/chip-counting friction while keeping attention on "the man or woman across from you" rather than the app [easy.poker marketing copy, undated — **vendor source, self-interested, but the design idea itself is verifiable by inspecting the app**].
- **Implication**: home-game apps intentionally de-emphasize casino chrome (avatars, spin wheels, ambient animation) in favor of a stripped, social-first, almost anti-skeuomorphic interface — the opposite direction from Zynga/PokerBros' more decorated, retention-gamified style.

### 6. Visual styles in market: casino-kitsch vs. modern flat
- **Casino-kitsch** (green felt, wood rail, gold trim, glossy skeuomorphic chips/buttons) is described in secondary sources as leaning on established casino color psychology — green for calm/money association, gold/yellow for wealth signaling in VIP/high-stakes contexts [thecommissionproject.com, "Casino Color Scheme: How Red & Gold Affect Players" — **marketing-psychology framing, not a design-research source, treat as color-association folklore rather than fact**]. This is the dominant style of Zynga Poker, PokerBros/PPPoker-style club apps, and older operator clients.
- **Modern flat/minimal** direction found described (secondary, generic) as: dark or pale-neutral ground, flat fills instead of skeuomorphic gloss, system-native accent color, a plain outlined table shape instead of a rendered felt-oval-plus-wood-rail graphic. No named shipping app was confirmed to fully embody this in the sources gathered — it appears more as a "redesign target" described by design case studies and generic UI-kit marketing than as a widely-shipped standard. **This is a gap**: we did not find a well-known, design-forward *live* poker product that is unambiguously "premium minimal" the way e.g. a fintech app would be — poker UI as a category still skews decorative/casino across every operator surveyed. That absence is itself informative for how we could differentiate.
- Two redesign case studies (Poker House by Limeup/Impltech; PokerStars work by Lackabane) both describe starting from a dated, cluttered original and moving toward "modern, elegant" — but neither published page gives concrete before/after visual specifics (palette, type), so treat as directional signal only, not a style reference. [limeup.io/impltech.co.uk "Poker House" case study, undated; lackabane.com PokerStars case study, undated — both **portfolio marketing copy, low technical detail**]

### 7. Recurring UX complaints (from app store reviews and secondary UX write-ups)
Primary (App Store review pages, sampled 2026-09-23):
- **Zynga Poker**: check/call misclick under time pressure (race between tap and incoming table-state update); abrupt disconnect that force-folds the hand with no apparent grace window; intrusive ads between hands; social/variety features praised.
- **PokerBros**: reviews sampled were almost entirely about perceived RNG unfairness, not UI — no misclick/clutter complaints surfaced in what we read.

Secondary (SEO/content-marketing sources — **lower confidence, patterns should be treated as plausible industry lore rather than verified fact until corroborated**):
- Small, closely-packed Fold/Call/Raise buttons causing misclicks is repeatedly named as *the* recurring poker-app UI complaint across multiple independent write-ups [vocal.media x2, isbobet88.com, sparktime.co.uk — all secondary content-marketing style sources, 2025/2026, no author credentials given beyond bylines].
- A specific failure mode named more than once: **Fold placed directly under/near Call**, causing accidental folds of hands players wanted to continue — cited as a known usability problem pattern (source: general web search synthesis, not one specific credited article — **weakest-confidence item in this note, flag as unverified pattern**).
- One PokerStars-specific review explicitly calls out **small Fold/Check buttons** as an error risk, while noting the action bar is adjustable [top10pokersites.com PokerStars review, undated — **affiliate review site, moderate confidence**].
- A claimed statistic — "cluttered interfaces contributed to abandonment rates rising above 44%" — appeared in one content-marketing search snippet with **no named source, study, or methodology**. **Do not cite this number; it is unverifiable and likely fabricated or badly telephone-gamed.** Flagged here only so it is not accidentally reused later.
- Recommended thumb-zone principle repeated across several secondary sources: put primary action buttons in the bottom half of the screen; ~75% of players are said to play one-handed (uncredited stat, same caveat as above) [vocal.media, "Why Mobile Poker Games Fail", ~2025].
- No credible primary-source discussion of **"timer anxiety"** specifically was found (searches for Reddit r/poker threads did not surface usable results — search tooling returned Wikipedia/App-Store noise instead of actual Reddit threads). This is a **research gap**, not a negative finding — treat "does a visible 30s countdown increase stress/mistakes" as an open question, not a settled complaint.

### 8. Four-color deck
- Widely offered as a **player-toggleable setting**, not a default, across online rooms: convention is spades = black, hearts = red, diamonds = blue, clubs = green [PokerNews "Four-Color Deck" glossary entry; Wikipedia "Four-color deck"; americascardroom.eu — glossary-level sources, high confidence on the convention itself, standard and uncontested across all sources checked].
- Rationale given consistently: faster suit differentiation at a glance, particularly valuable on small mobile screens and when multi-tabling [ggpoker.com blog; pokervip.com].
- GGPoker's own guidance recommends new mobile players **turn it on before their first hand** — i.e. treats it as a mobile-legibility feature, not a cosmetic option [ggpoker.com blog, undated].
- Live/physical play still defaults to standard two-color decks, so four-color is specifically an **online/digital-legibility convention**, which supports adopting it (as an opt-in or even default) for a mobile-first product.

## comparison table

| app / product | table orientation on phone | bet sizing controls | 4-colour deck | timer treatment | visual style | source confidence |
|---|---|---|---|---|---|---|
| PokerStars | portrait + landscape, switchable | not detailed; action bar "adjustable"; small Fold/Check buttons flagged as an error risk | offered (customization suite) | not found | casino-standard, heavily customizable (table themes, decks) | low-medium (agency portfolio + affiliate review, no primary UI spec) |
| GGPoker | portrait (1-hand) or landscape ("full view"); portrait positioned as adequate mainly for single-tabling | presets (1/3, 1/2, 3/4, pot) + slider + manual entry; "Smart Betting" AI suggestions; multi-table swipe-to-fold | offered, recommended on by default guidance | not found | mobile-first minimized flash ("plain but functional" tile view); Smart HUD unique to mobile | medium (operator blog + trade press, no live-app inspection) |
| partypoker | portrait since Nov 2019 relaunch | preset amounts + upward-swipe slider | not found in sources | not found | "vibrant graphics" per trade press | low-medium (trade press only) |
| 888poker | portrait since Android overhaul; grid lobby | not detailed | not found | not found | not detailed | low (trade press headlines only) |
| Zynga Poker | not detailed (social app, casual) | not detailed | not found | not found; disconnect = abrupt force-fold, no stated grace period | decorative/social: avatars, themed decks, buddy system | medium (primary: App Store reviews) |
| WSOP (social) | not verified (FAQ page blocked automated fetch, HTTP 403) | not found | not found | not found | not found | very low — essentially unverified, gap |
| PokerBros | vertical mobile layout | not detailed | not found | adjustable elsewhere in category (ClubGG: 13-25s + time bank) | customizable avatars/themes, social/chat-heavy | medium (primary: App Store reviews, but reviews focus on RNG trust not UI) |
| ClubGG | vertical mobile layout, mirrors GGPoker | not detailed | inherited from GGPoker network presumably (not confirmed) | 13-25s adjustable + time bank | mirrors GGPoker's clean mobile look | low-medium (affiliate/comparison sites) |
| Poker Now (home game) | browser-based, responsive; not a fixed "table graphic" description found | not detailed | not found | not found | built-in video/voice tiles instead of avatar table | low (vendor site) |
| EasyPoker (home game) | vertical, "hold like a hand of cards"; explicitly not top-down table view | not detailed | not found | not found | deliberately stripped/anti-skeuomorphic, no avatars/wheel/bells | low (vendor site, but design claim is independently checkable) |

## implications for our design system

**Patterns to adopt:**
1. **Portrait-first, single-hand-operable table is table stakes in 2026** — every major operator moved there years ago. v1.0 must design portrait-first with landscape as a secondary/optional mode, not the reverse.
2. **Bet sizing: presets as the primary control, slider as secondary, numeric entry as a fallback.** Multiple independent sources converge on sliders being the most misclick-prone control; GGPoker's own guidance recommends against relying on the slider alone. Use ½-pot/pot-style presets (adapted for play-chip, no currency symbols) as the fast path.
3. **Keep primary action buttons (fold/check/call/bet/raise) large, separated, and anchored in the bottom "thumb zone."** This is the single most repeated complaint across sources (even though several of those sources are lower-confidence content marketing, the same failure mode — Fold too close to Call — is independently named by a primary PokerStars review too). Concretely: never place Fold directly adjacent to Call/Check with no gap or confirmation.
4. **Design an explicit, visible disconnect/reconnect grace state.** Zynga's harshest reviewed complaint is a hand getting force-folded on a bump with no visible warning — directly relevant to the v1.0 "disconnected" seat state requirement. A visible countdown/grace period before auto-fold-on-disconnect is a concrete, low-risk win.
5. **Offer four-color deck, and default it on (or prompt for it) rather than burying it as an obscure setting** — this is a near-universal, uncontested online-poker convention specifically justified by small-screen legibility, which is exactly our context.
6. **Consider the "home game" visual register, not just the "online cardroom" register, for at least the tone of v1.0/v1.1.** EasyPoker's explicit rejection of avatars/wheels/casino chrome lines up with our own scope: no money vocabulary, no fairness badges yet, "friends playing on phones" as the actual use case (v1.1) rather than a simulated Vegas casino. This is a legitimate visual-direction candidate: stripped, calm, card-table-not-casino-floor.
7. **Queue/lock the action button during opponent action resolution**, or otherwise prevent the Zynga-style race condition where a tap registers against a table state that changed a moment earlier (check → interpreted as call). This is a state-machine/interaction detail worth carrying into the spec, not just visual design.

**Patterns to avoid:**
1. Small, tightly-packed action buttons — named directly as an error source by both a primary PokerStars review and repeated secondary sources.
2. Bare slider as the only or primary bet-sizing input.
3. Abrupt state transitions on disconnect with no visible warning/grace window.
4. Decorative clutter competing with the four gameplay-critical zones (cards, pot/board, stacks, action bar) — repeated across every UX-focused source, regardless of confidence level.
5. Casino-kitsch as a default assumption purely because it's the incumbent look — it is the *most common* style in market, not the only credible one, and the research found no shipped "premium minimal" poker product to benchmark against — meaning a clean, non-casino direction would be more differentiated, not just "safer."

## open questions
- What are the actual seat-position coordinates/arc patterns for 6-max and 9-max on a phone in the apps surveyed? No source gave this at a usable level of detail — this likely requires direct app inspection (screenshots/screen recording) rather than web research, since operators don't publish layout specs.
- Does a visible 30-second countdown timer measurably increase misclicks or player-reported stress ("timer anxiety")? Not found in any source; the target Reddit/2+2 search did not surface usable primary discussion (search tooling limitation, not confirmed absence of the phenomenon).
- What does the WSOP app's table UI actually look like? Its own FAQ page blocked automated fetch (HTTP 403); would need a different retrieval method (e.g. direct app inspection, cached page, or a differently-worded search).
- Is there a genuinely premium/minimal, well-known poker product we're missing? This research turned up redesign *case studies* aspiring to "modern and elegant" but no widely-recognized live app that unambiguously ships that look — worth a targeted follow-up search (e.g. specific fintech-adjacent or crypto-poker competitor UIs, which sit closer to our own product).
- The "44% abandonment" and "75% one-handed" statistics surfaced in secondary sources are uncredited and should not be treated as fact; if a real number is needed for a design brief, it should be sourced from a named study or left out.

## what would change these conclusions
- Direct hands-on inspection (screenshots or screen recording) of PokerStars, GGPoker, WSOP, Zynga Poker and PokerBros on an actual phone would immediately upgrade most "not detailed"/"not found" cells in the comparison table from low to high confidence — this is the single highest-value follow-up.
- A working Reddit/2+2 search (current tooling failed to surface real threads) could confirm or deny the "timer anxiety" and "Fold-next-to-Call" claims with primary player testimony instead of secondary paraphrase.
- Finding a credited, dated UX case study (not portfolio marketing copy) for any operator's mobile redesign would let us replace several "low confidence, marketing framing" citations with genuine design-research sources.

## related
[[ui_design_system]] (initiative) · [[roadmap]] · [[vision]]
