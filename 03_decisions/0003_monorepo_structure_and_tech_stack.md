---
type: decision
status: accepted
date: 2026-09-23
supersedes: 
superseded_by: 
initiative: v1_0_casual_multiplayer_poker
tags: [decision]
---

# 0003 — monorepo structure and tech stack

<!-- status: proposed | accepted | superseded | rejected — only the owner flips proposed → accepted -->

## context
[[v1_0_casual_multiplayer_poker]] needs a poker engine, a real-time server and a mobile-first web client, built mostly by AI agents and operated by one person. `../poker-monorepo` is empty and [[0001_split_docs_vault_and_code_monorepo]] left its internal structure to a later ADR — this one.

Owner direction gathered on 2026-09-22/23 (grilling session):
- **TypeScript only** for the whole repository — backend, frontend and any future mobile client.
- **Turborepo** preferred as the monorepo tool.
- **Socket.IO** for real-time transport and reconnection.
- **v1.0 keeps game state in memory**; a deploy kills every running table. A persistent store (Redis-like) is planned for later, so state access must sit behind an interface from day one.
- **Hand histories are logged from day one** — the only way to notice engine bugs in real play, and the raw material v3.0 verification will need.
- **Self-hosted on a small VPS/PaaS instance.** Region and jurisdiction are explicitly *not* a v1 concern (owner, 2026-09-23); the provider is chosen at deploy time.
- The shuffle stays behind a single interface (cheap insurance for [[v3_0_provable_randomness]]).
- No headless bot player in v1.0 scope.

**Assumption:** a single Node process on one instance handles every v1.0 table with headroom; the target is 1,000 hands total, not thousands of concurrent players.

## options considered
1. **Turborepo + pnpm workspaces, strict package boundaries (engine / protocol / server / web)** — the engine is a pure, IO-free package testable in isolation and importable by the client for UI-side validation; shared event types keep server and client honest; Turborepo caches builds and tests across packages. Cost: more scaffolding than a single app.
2. **Single Node app with folders instead of packages** — least ceremony; but nothing stops the engine from growing IO tentacles, the client can't share the engine, and splitting later is expensive once agents have written to loose boundaries.
3. **Nx or a plain pnpm workspace without a task runner** — comparable to option 1; Nx is heavier and the owner named Turborepo. A bare workspace loses cached, dependency-aware `test`/`build` pipelines that agents rely on for fast feedback.

Framework choices inside option 1 (recommendations, not owner mandates):
- **Client:** React + Vite, mobile-first, single-page. Rationale: largest TypeScript ecosystem, trivial Socket.IO integration, no server-rendering needed for a real-time table.
- **Server:** Node (current LTS) + Socket.IO; a thin HTTP layer only for health checks and the static client.
- **Tests:** Vitest across all packages; engine tests assert against numbered requirements in a rules spec (`04_specs/`, to be written by `poker-rules-analyst`).
- **Packaging/deploy:** one Docker image serving client + server; any small VPS/PaaS that runs a container.

## decision
Option 1. `../poker-monorepo` is a **Turborepo + pnpm** workspace, **TypeScript everywhere**, laid out as:

```
apps/
  web/        React + Vite mobile-first client (Socket.IO client)
  server/     Node + Socket.IO; tables, seats, matchmaking, timers
packages/
  engine/     pure TS poker engine: rules, hand evaluator, betting state machine.
              No IO, no timers, no network. Deterministic given (state, action, shuffle).
  protocol/   shared types: client→server actions, server→client events, table snapshots
  config/     shared tsconfig / eslint / prettier presets
```

Interfaces fixed now because later versions swap their implementations:
- `Shuffler` (in `engine`): produces a deck order. v1.0 = CSPRNG server shuffle; v3.0 replaces it.
- `TableStore` (in `server`): load/save table state. v1.0 = in-memory map; later = Redis or similar.
- `HandHistorySink` (in `server`): receives one immutable record per completed hand. v1.0 = append-only JSONL on disk (rotated), no personal data beyond nicknames.

Hosting: one Docker container on a small VPS/PaaS chosen at deploy time; provider is *not* fixed by this ADR (owner: irrelevant for v1). The README must let a stranger run the whole stack locally with `pnpm install && pnpm dev`.

## consequences
- Agents can develop and test the engine without a browser or a server; test vectors from the rules spec become the engine's acceptance suite.
- Client and server share one `protocol` package, so drift between them fails type-checking instead of production.
- In-memory state means **every deploy ends every table** in v1.0. Accepted by the owner; `TableStore` keeps the door open for persistence without touching the engine.
- Hand histories exist from the first real hand; they are also the first place to look when the 1,000-hands exit criterion for v1.0 is challenged.
- TypeScript-only rules out, for now, Rust/Go for the engine even if v3.0 cryptography would prefer them; a v3.0 scheme may still be implemented behind `Shuffler` in TS or via a separate service.
- Framework picks (React, Vite, Vitest) are recommendations — if the owner amends them the layout above stands.

## revisit when
- A persistent `TableStore` is needed (v1.1 same-room play makes deploy-kills-table painful, or hand count > 1,000).
- v3.0's chosen randomness scheme cannot be implemented behind `Shuffler` in TypeScript.
- A mobile client beyond the web app is scoped (evaluate React Native / Expo inside the same workspace).
- A second contributor joins and package boundaries need enforcement (eslint import rules, CODEOWNERS).
