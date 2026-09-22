---
name: new-initiative
description: Create an initiative note for an outcome-sized piece of work in poker-docs. Use when the user wants to start, plan, or track a new piece of work, or when a backlog item has outgrown a checkbox.
---

# new-initiative

An initiative is an outcome with acceptance criteria — not a single task and not a theme.

1. Clarify only what's missing: the **goal** (what is true when done), **why**, **priority** (p0–p3), and rough **scope in/out**. If the user gave enough, don't ask.
2. Search `02_initiatives/` (both `ongoing/` and `past/`) and `backlog.md` for an existing/overlapping one. Prefer extending it; if you promote a backlog item, remove it from `backlog.md`.
3. Create `02_initiatives/ongoing/<slug>.md` from `_templates/initiative.md` (slug: lowercase, underscores, no dates). Fill frontmatter (`status: proposed` unless the user says start now → `active`; `milestone` if it maps to one in `roadmap.md`) and every section. `done when` items must be checkable.
4. Add it to `roadmap.md` under now/next/later.
5. Link related decisions, specs and research if they exist. Add a first `## log` line dated today.
6. Update `status.md` if it changes current focus or next actions.

Report the path and the goal in one line.
