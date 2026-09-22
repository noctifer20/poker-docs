---
type: initiative
status: proposed
priority: p2
milestone: v3.0
created: 2026-09-22
target: 
tags: [initiative]
---

# v3_0_provable_randomness

## goal
Every shuffle and deal is verifiable by players per a numbered spec in `04_specs/`, with a verification UI, and the scheme has passed external review. This is the gate before any real-value play.

## why
This is the product's whole promise ("provably random"). Until it exists nothing may be claimed and no real value may move.

## scope
**in:**
- resume [[randomness_scheme_research]] → randomness scheme ADR, adversarially reviewed by `fairness-reviewer`
- spec in `04_specs/` (`spec-writer`) with numbered requirements and a verification path per requirement
- implement the scheme behind the engine's shuffle interface
- verification UI: a player can check a hand's shuffle/deal from public data
- external audit

**out:**
- real-value launch itself — separate gate after v3.0: audit findings closed, jurisdiction plan executed

## done when
- [ ] randomness scheme ADR accepted
- [ ] spec at `stable` in `04_specs/`
- [ ] a third party can reproduce and verify any hand's shuffle from public data
- [ ] verification UI shipped
- [ ] external audit complete; findings tracked

## tasks
- [ ] Resume [[randomness_scheme_research]]
- [ ] ADR via `/new-decision` (calls `fairness-reviewer`)
- [ ] Spec via `spec-writer`
- [ ] Implementation behind the shuffle interface + verification UI
- [ ] External audit

## open questions
<!-- not decided — ask the owner before assuming -->
- which scheme family (research parked until v1.1 ships)
- what "verify" looks like for a non-technical player

## decisions
- [[0002_staged_delivery_free_play_first]] (accepted 2026-09-22)

## related
[[roadmap]] · [[vision]] · [[randomness_scheme_research]] · [[glossary]]

## log
- 2026-09-22 — created from the owner's staging brief.
