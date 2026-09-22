---
type: decision
status: accepted
date: 2026-09-22
supersedes: 
superseded_by: 
initiative: 
tags: [decision]
---

# 0001 — split docs vault and code monorepo

## context
The project needs both a place to track status, decisions and specs (heavily maintained by AI agents) and a place for source code. The sole contributor works in Obsidian for notes and Claude Code for both.

## options considered
1. **One repo, docs inside the monorepo** — single history, but mixes note churn (daily logs, status) with code history and forces Obsidian to index build artifacts.
2. **Docs vault nested inside the code repo, git-ignored** (the pattern used for another project) — convenient but hides the code from the vault's own history.
3. **Two sibling folders/repos: `poker-docs` (Obsidian vault) and `poker-monorepo` (code)** — clean histories, each tool sees only what it needs.

## decision
Two sibling folders in the workspace: `poker-docs/` (Obsidian vault, project state and decisions) and `poker-monorepo/` (source code). Each is its own git repository. Cross-references use paths, commits and PRs, not copied code.

## consequences
- Vault stays fast and note-focused; code history stays free of note churn.
- Agents working across both must update state in the vault when code work changes reality.
- The monorepo's internal structure and tooling are a separate, later decision.

## revisit when
Cross-repo drift (docs and code disagreeing) becomes a recurring problem, or a second contributor joins.
