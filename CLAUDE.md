# poker-docs — project brain for the provably random poker game

This is an **Obsidian vault** and the single source of truth for project *state, context and decisions*. Code lives next door in `../poker-monorepo` (see "code repo"). I'm the only human contributor; AI agents (you) do a large share of the planning, writing and bookkeeping here, so this vault is designed to be **read fast at the start of a session and left accurate at the end of one**.

## the project

A **provably random poker game powered by cryptocurrency**: players can independently verify that shuffles/deals were fair, and value moves in crypto. Details, principles and non-goals live in [[vision]] — read it, don't rely on this summary. Everything technical (randomness scheme, chain, custody, game server model) is **undecided** unless a note in `03_decisions/` says otherwise.

## session protocol

**At the start of a session** (or run `/session-start`):
1. Read `status.md` — the current-state snapshot. Trust it, but verify anything load-bearing against the notes it links to.
1. Read `awaiting_owner_review.md` — everything blocked on me. Don't re-ask what's already listed there; don't guess at it either. When I want to work through it, run `/owner-review`.
2. Read `roadmap.md` (now/next) and the notes in `02_initiatives/ongoing/` with `status: active` or `blocked`.
3. Skim today's/latest note in `01_daily/` and count what's in `00_inbox/`.
4. Then do the work. If the request touches a domain term you're unsure of, check `glossary.md`.

**At the end of a session** (or run `/session-end`):
1. Append a line to today's daily note under `## 🤖 ai sessions` (create the note from `_templates/daily_note.md` if missing).
2. Update `status.md` — bump `updated:`, refresh *current focus*, *recent changes*, *blockers*, *next actions*. Keep it short; it is a snapshot, not a log.
3. Tick finished tasks, add newly discovered ones to the right initiative (or `backlog.md`), and add a dated line to the initiative's `## log`.
4. If work finished an initiative: set `status: done` and move it to `02_initiatives/past/`; update `roadmap.md`.
5. Update `awaiting_owner_review.md`: add every new item that needs my decision, confirmation or answer (with a link and the date); move items I resolved this session to *resolved recently*.

A session that changes project reality but leaves `status.md` or `awaiting_owner_review.md` stale is an incomplete session.

## structure

```
home.md            dashboard (dataview) — for me, not for you
status.md          living snapshot of where the project stands — AI-maintained
awaiting_owner_review.md  queue of everything blocked on my decision/answer — AI adds, I resolve
vision.md          what/why, principles, non-goals — change only with my approval
roadmap.md         milestones + now / next / later / done
backlog.md         unscoped tasks not yet in an initiative
glossary.md        domain terms (fairness, crypto, poker) — keep definitions precise
00_inbox/          untriaged capture; sweep with /triage-inbox
01_daily/          YYYY-MM-DD.md day log; weekly/ holds gggg-[w]ww.md reviews
02_initiatives/    ongoing/ and past/ — one note per outcome-sized piece of work
03_decisions/      ADRs: NNNN_slug.md, numbered, immutable once accepted
04_specs/          our designs and protocol specs (the things code is built from)
05_research/       external knowledge: papers, competitors, regulation, tech evals
06_archive/        inactive material; kept for search, not for use
_templates/        note templates
attachments/       images and files
.claude/skills/    workflow commands: session-start, session-end, owner-review, new-initiative, new-decision, triage-inbox, weekly-review
```

## conventions

**Naming:** lowercase, underscores instead of spaces, for every file and folder. Exceptions: dated notes (`2026-09-22.md`, weekly `2026-w39.md`) and ADR numbers (`0007_use_commit_reveal.md`, zero-padded, next free number).

**Frontmatter:** every note has `type` and `tags` matching its template. Add fields, never remove ones you don't understand. Status vocabularies:

| type | statuses |
|---|---|
| `initiative` | `proposed` → `active` ⇄ `blocked` → `done` \| `dropped` |
| `decision` | `proposed` → `accepted` \| `rejected`; later `superseded` |
| `spec` | `draft` → `review` → `stable` → `deprecated` |

Initiatives also carry `priority: p0…p3` (p0 = drop everything), optional `milestone` and `target` date.

**Tasks** are markdown checkboxes in the Tasks-plugin format: `- [ ] do the thing 📅 2026-10-01 ⏫`. Tasks live inside their initiative note; only orphans go in `backlog.md`. Don't invent a second task system.

**Links:** link liberally with `[[wikilinks]]`. Initiatives link their decisions, specs and research; decisions link back to the initiative; status links to everything it mentions.

## rules that matter more than usual here

- **Decisions before commitments.** Anything touching the randomness/fairness scheme, cryptography, chain choice, custody of funds, or jurisdiction gets an ADR (`/new-decision`) *before* code depends on it. You may draft and recommend; **only I flip `proposed` → `accepted`.** Accepted ADRs are never edited except to mark them superseded — write a new one instead.
- **Everything waiting on me goes in `awaiting_owner_review.md`.** Proposed ADRs, edits to `vision.md`, open questions you must not guess at, inbox to triage. One line per item, linked, dated. If it isn't there, I won't see it; if it is there, don't ask me again in chat unless it blocks the current task.
- **No unearned fairness claims.** "Provably fair/random" is the product's whole promise. Never write that a property *holds* unless a spec in `04_specs/` states it as a numbered requirement and there is a verification path (test, proof or audit). Unproven beliefs are labelled **assumption**.
- **Cite research.** Notes in `05_research/` need sources (URL/paper + retrieval date). Say when something is from memory and unverified.
- **Regulation is a first-class risk.** Real-money gambling and crypto are regulated and jurisdiction-dependent. Track it in `05_research/` and `status.md` risks; never present anything as legal advice.
- **No secrets in the vault.** No keys, seed phrases, API tokens, credentials or wallet addresses that hold funds. Reference where they live instead.
- **Don't duplicate code here.** Point at `../poker-monorepo` paths, commits or PRs. Specs describe *what must hold*; the repo holds *how*.

## how to help me here

- When I say "log", "capture", "jot down" → new note in `00_inbox/` from `inbox_capture.md` unless I clearly name a destination.
- Prefer updating an existing note over creating a near-duplicate. Search first.
- Be candid in `status.md`: blockers and risks belong there even when they're uncomfortable.
- Never delete notes or move them out of the vault without asking. Moving initiatives `ongoing/` → `past/` on completion is the one routine exception.
- Don't edit `.obsidian/`, `.makemd/` or `.space/` (plugin state), except when I ask for config changes.
- Follow the naming and frontmatter conventions for anything you create; use the templates in `_templates/` as the schema.

## subagents

`.claude/agents/` has specialists for this project's real risks — dispatch them rather than doing the work inline:

| agent | use for |
|---|---|
| `fairness-reviewer` | adversarial review of any randomness/crypto/fairness design before it's proposed for acceptance |
| `researcher` | cited technical research (protocols, chains, libraries, competitors) into `05_research/` |
| `regulatory-analyst` | jurisdiction/licensing/compliance research into `05_research/` — not legal advice |
| `spec-writer` | turn an accepted ADR into a numbered spec in `04_specs/` |
| `poker-rules-analyst` | game rules, edge cases, test vectors; checks the protocol actually supports real play |
| `vault-auditor` | read-only consistency/honesty check of the vault itself |
| `design-reviewer` | adversarial review of design directions, the Claude Design system, token sets, the ui spec and built screens — before anything goes to the owner for approval |

Code-side agents live in `../poker-monorepo/.claude/agents/` (they load when Claude runs from the monorepo): `platform-engineer` (build, Docker, CI, Dokploy/Cloudflare deploy runbooks — branches only, never deploys) and `security-reviewer` (read-only review of the server's attack surface and deploy config, before any deploy that touches them).

`/new-decision` calls `fairness-reviewer` automatically when relevant; `/weekly-review` calls `vault-auditor`. Reach for the others whenever their trigger fits, even mid-session.

## code repo

`../poker-monorepo` — the source code monorepo (currently empty; structure decided later, record it in an ADR). This vault is git-tracked separately from it. When work spans both, do the code in the monorepo and record the outcome (initiative log, status, ADR) here.

## git & backup

The vault is a git repo backed up by `obsidian-git`. Check `git status` before broad edits. Commit only when I ask; never push or force-push without asking.

## plugins in use

`make-md` (folder/space views) · `dataview` (powers `home.md` and queries) · `obsidian-tasks-plugin` (due dates, priorities) · `templater-obsidian` (dynamic templates; scripts folder `_templates/scripts`) · `quickadd` (choices: capture to inbox `Alt+Z`, new decision, new initiative) · `periodic-notes` (weekly notes) · `calendar` · `obsidian-git` · `claude-sidebar`.
