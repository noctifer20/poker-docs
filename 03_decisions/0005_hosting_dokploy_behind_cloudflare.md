---
type: decision
status: accepted
date: 2026-09-27
supersedes: 
superseded_by: 
initiative: v1_0_casual_multiplayer_poker
tags: [decision]
---

# 0005 — hosting: Dokploy on the owner's VPS behind Cloudflare

<!-- status: proposed | accepted | superseded | rejected — only the owner flips proposed → accepted -->

## context
[[0003_monorepo_structure_and_tech_stack]] fixed the shape of production: **one Docker container serving client + server** on "a small VPS/PaaS chosen at deploy time". v1.0 now needs a public URL so testers can play toward the 1,000-hand exit ([[v1_0_casual_multiplayer_poker]]). This ADR picks the host and the shape of the public entry point.

Facts:
- The owner already runs **Dokploy** (self-hosted PaaS; Traefik as its reverse proxy) on their own VPS, and manages DNS for **`noctifer20.com`** in **Cloudflare**. The game goes on a subdomain of it.
- `apps/server` already serves the built `apps/web/dist` from the same Express/Socket.IO process (`apps/server/src/index.ts`), so client and socket share one origin with no extra routing.
- Game state is in memory: **one replica only**; a restart voids hands in progress (R53, and a consequence already accepted in ADR 0003). Hand history is append-only JSONL on disk (`HAND_HISTORY_PATH`).
- The built server currently fails to start (`node dist/index.js` can't import `@poker/engine`, which is consumed from TS source). That's a build fix, not a hosting question, but it blocks any deploy.

Owner direction, 2026-09-27: one subdomain; Cloudflare proxy **on**; deploys killing live tables is **fine for v1.0**, with a maintenance/drain mode as a follow-up after v1.0 (task in [[v1_1_private_lobbies_for_friends]]).

**Assumptions** (from memory, unverified — `platform-engineer` must check current docs before relying on them):
- Cloudflare's proxy passes WebSockets on all plans, with an idle timeout around 100 s; Socket.IO's default 25 s heartbeat keeps connections well inside it.
- Cloudflare's SSL/TLS encryption mode is a **zone-wide** setting (overridable per hostname with configuration rules), so choosing "Full (strict)" for the game may affect other `noctifer20.com` subdomains.
- Dokploy/Traefik can obtain a Let's Encrypt certificate for a proxied hostname, or accept an uploaded Cloudflare Origin CA certificate instead.

## options considered

### A. Where it runs
1. **Dokploy on the owner's VPS** — pros: already running and paid for; git-based builds from a Dockerfile, env vars, volumes, domains and TLS in one UI; no new account. Cons: the owner is the ops team (patching, disk, backups); one machine is a single point of failure — acceptable for a free play-chip beta.
2. **Managed container PaaS (Fly.io, Render, Railway…)** — pros: no server to maintain, easy TLS. Cons: a new account and bill; sleeping/free tiers are bad for a long-lived WebSocket server; persistent disk for hand history costs extra. Nothing here that option 1 lacks for v1.0.

### B. Public entry point
1. **One subdomain, one container, same origin** (`poker.noctifer20.com` → the server, which serves the web app and `/socket.io`) — pros: no CORS beyond "same origin", simplest cookies/tokens, one TLS cert, matches ADR 0003's one-container shape. Cons: can't scale web and socket separately — irrelevant with one in-memory replica.
2. **Two subdomains** (`poker.` for static web, `api.poker.` for the socket) — pros: web could move to a CDN/static host. Cons: cross-origin config, two routes and certs, for no v1.0 benefit.

### C. Cloudflare proxy
1. **Proxied (orange cloud)** — pros: hides the VPS IP from casual lookup, TLS at the edge, basic DDoS/abuse shielding, caching of static assets. Cons: one more hop and timeout to respect for WebSockets; the app must take the client IP from Cloudflare's header and should refuse traffic that bypasses Cloudflare, or rate limits are meaningless; zone-wide TLS mode interacts with other subdomains.
2. **DNS only (grey cloud)** — pros: simplest; Traefik handles TLS alone. Cons: origin IP public, no edge shielding.

### D. How deploys are triggered
1. **Manual deploy in Dokploy from `main`** — pros: a deploy voids every live hand, so it should be a deliberate act between tester sessions. Cons: one click per release.
2. **Auto-deploy on push to `main`** — pros: hands-off. Cons: any merge kills tables mid-session. Rejected for v1.x.

## decision
**A1 + B1 + C1 + D1.**

- **Host:** a Dokploy *Application* on the owner's VPS, built from the repo's `Dockerfile` (repo `github.com:noctifer20/poker`, branch `main`), **one replica**, deployed **manually** — never auto-deploy on push.
- **URL:** **`poker.noctifer20.com`** (confirmed by the owner, 2026-09-27). Cloudflare DNS record → the VPS, **proxied**. Single origin: the container serves the web app, `/socket.io` and `/healthz`.
- **TLS:** Cloudflare "Full (strict)" for this hostname, with a valid certificate on the origin (Let's Encrypt via Dokploy/Traefik, or a Cloudflare Origin CA certificate) — the exact method is chosen and verified by `platform-engineer`; if the zone-wide mode would change other subdomains, scope it with a configuration rule.
- **State & data:** `HAND_HISTORY_PATH` on a **Dokploy persistent volume**, rotated; never served over HTTP. Game state stays in memory.
- **Security baseline before the first public deploy:** Socket.IO/Express restricted to the `https://poker.noctifer20.com` origin; payload and rate limits; client IP from Cloudflare's header only when the request came through Cloudflare; origin not reachable around Cloudflare (firewall to Cloudflare IP ranges, or equivalent). Reviewed by the monorepo's `security-reviewer` agent.
- **Who does what:** `platform-engineer` (monorepo `.claude/agents/`) writes the Dockerfile, CI and a deploy runbook in the README; the owner makes Dokploy, Cloudflare and GitHub changes unless they hand an agent scoped access for a session. No credentials in either repo or the vault.

## consequences
- The first deploy is a short list of owner steps rather than new infrastructure; hosting costs nothing extra.
- Every deploy still ends every table (ADR 0003). Deploy between tester sessions until a maintenance/drain mode exists (stop new seats, let hands finish, then restart) — tracked in [[v1_1_private_lobbies_for_friends]].
- Rate limiting and abuse handling must be written for the Cloudflare → Traefik → Node chain; getting the client IP wrong silently disables them.
- The VPS is now a production dependency: its patching, disk space and the Dokploy dashboard's exposure are the owner's responsibility. Hand histories on the volume are the only durable data — back up the volume if they matter for the 1,000-hand exit evidence.
- Nothing here touches money, custody or randomness, so it carries no fairness weight; v2.0 (wallets) will need its own hosting/secrets review.

## revisit when
- A second replica is needed (persistent `TableStore` lands) — then sticky sessions or a shared store, and zero-downtime deploys, become questions.
- v2.0 starts: accounts, secrets and wallet infrastructure need a hosting and key-management decision of their own.
- The VPS runs out of headroom, or an outage costs a tester session.
- Cloudflare's WebSocket limits or TLS behaviour turn out different from the assumptions above.
