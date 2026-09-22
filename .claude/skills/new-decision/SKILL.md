---
name: new-decision
description: Draft an architecture/decision record (ADR) in poker-docs. Use for any choice about the randomness/fairness scheme, cryptography, chain, custody, game-server model, jurisdiction, or tooling — before code depends on it.
---

# new-decision

Only the owner accepts a decision. You draft, research, and recommend.

1. Find the next number: highest `NNNN_` in `03_decisions/` + 1, zero-padded to 4 digits. Search existing ADRs for overlap; if this reverses one, you're writing a superseding ADR (step 5).
2. Create `03_decisions/NNNN_<slug>.md` from `_templates/decision.md` with `status: proposed`, today's date, and `initiative:` set if one drives it.
3. Fill the sections honestly:
   - **context** — forces and constraints; label unverified beliefs **assumption**.
   - **options considered** — at least two real options with pros/cons; cite `05_research/` notes or sources for factual claims.
   - **decision** — state your recommendation as the proposed choice.
   - **consequences** and **revisit when** — concrete triggers, not "if things change".
4. Link it from the driving initiative (`## decisions`) and mention it under *decisions* in `status.md` as open.
5. If superseding: set `superseded_by` on the old ADR and `status: superseded` — that is the only edit an accepted ADR gets — and set `supersedes` on the new one.
6. Before telling the user it's ready: if the decision touches randomness, cryptography, custody, or the fairness protocol, dispatch the `fairness-reviewer` subagent on the draft and fold its findings in (or list them as open risks if you disagree).

Tell the user it's waiting on them; if they accept, set `status: accepted` and update `status.md`.
