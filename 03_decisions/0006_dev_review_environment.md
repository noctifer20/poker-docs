---
type: decision
status: proposed
date: 2026-09-29
supersedes: 
superseded_by: 
initiative: v1_0_casual_multiplayer_poker
tags: [decision]
---

# 0006 — a `dev` review environment that deploys itself

<!-- status: proposed | accepted | superseded | rejected — only the owner flips proposed → accepted -->

## context
[[0005_hosting_dokploy_behind_cloudflare]] runs production at `poker.noctifer20.com`, deployed **by hand** from `main` (D1), because a deploy voids every live hand (R53). It rejected auto-deploy **for `main`** only.

That leaves no place to look at a change before it reaches testers. Today "try it" means running a feature branch locally (e.g. `feat/playtest-feedback-1` in [[awaiting_owner_review]]), which doesn't show real phones, the Cloudflare path or other people at the table.

Owner direction, 2026-09-29 (chat): add a `dev` branch; merging into `dev` deploys it to a dev hostname automatically; **every change that needs the owner's manual review goes to `dev` first**, and only after the feedback is it decided whether it goes to `main`.

Facts checked 2026-09-29:
- Cloudflare's free Universal SSL certificate covers the apex and **one** level of subdomain; `dev.poker.noctifer20.com` would get no valid certificate without Advanced Certificate Manager (https://developers.cloudflare.com/ssl/edge-certificates/universal-ssl/limitations/, retrieved 2026-09-29). The owner chose `poker-dev.noctifer20.com` instead of paying (2026-09-29).
- Dokploy can queue a deploy through `POST /api/application.deploy` with an `x-api-key` header and `{applicationId}` (https://docs.dokploy.com/docs/core/auto-deploy, retrieved 2026-09-29). Its own auto-deploy is a webhook from GitHub. **Both need the Dokploy panel reachable from GitHub**, and it is deliberately reachable only over the owner's tailnet (owner's server notes, 2026-09-27).

## options considered

### A. Hostname
1. **`poker-dev.noctifer20.com`** — free certificate, same edge setup as production. Chosen by the owner.
2. **`dev.poker.noctifer20.com`** — the name first asked for; needs ACM (~$10/month) for an edge certificate.

### B. What triggers a dev deploy
1. **GitHub Actions on push to `dev`: build/test/lint, then call the Dokploy API** — a red build never reaches dev; needs the panel's API reachable from GitHub (see C).
2. **Dokploy's own auto-deploy (GitHub webhook)** — no workflow to maintain; deploys even when tests fail; still needs Dokploy reachable from GitHub.
3. **A timer on the VPS that polls the `dev` branch and calls the local Dokploy API** — nothing exposed; up to a minute of lag, custom code on the VPS, harder to see.

### C. How a passed build reaches Dokploy
1. **GitHub moves a `dev-deploy` branch; a timer on the host watches it** (B1 + B3 combined) — only checked commits deploy, nothing new exposed; up to a minute of lag, a small script on the host.
2. **Panel on a proxied hostname behind Cloudflare Access, called by GitHub with a service token** — undoes the tailnet-only lockdown of the panel; more setup.
3. **Re-open the panel publicly** — rejected; it was closed on 2026-09-27 for good reason.

### D. Branch flow
1. **Feature branch → `dev` for review → the feature branch (not `dev`) → `main`** — rejected or pending work never rides along to production; `dev` needs a revert when something is rejected.
2. **`dev` → `main` as a whole** — simpler merges, but every rejected change must be reverted before any release.

## decision
**A1 + B1 + C1 + D1** (C1 replaced the first draft's Cloudflare Access idea on 2026-09-29, once the panel's tailnet-only setup was read).

- **Branches:** `main` = production (manual deploy, unchanged from ADR 0005). `dev` = review environment at **`https://poker-dev.noctifer20.com`**, deployed automatically on every push to `dev` once build/test/lint pass: `.github/workflows/dev.yml` fast-forwards `dev-deploy` to the checked commit; a systemd timer on the host (`deploy/dev-poller/` in `../poker-monorepo`) sees it within a minute and queues the Dokploy deploy on the local API. `dev-deploy` is written only by CI.
- **Convention:** every change the owner has to look at before it ships goes through `dev`: branch off `main` → merge into `dev` → owner reviews on poker-dev → approved: merge the **feature branch** into `main`, deploy production by hand, merge `main` back into `dev`; rejected: `git revert -m 1` the merge on `dev`. `dev` is never force-pushed or reset. Changes with no visible effect (docs, tests, refactors) may go straight to `main`.
- **Dev app:** a second Dokploy application (project `poker`, environment `dev`) built from `dev-deploy` with the same `Dockerfile`, **one replica**, its **own volume** and its **own** `PROXY_SHARED_SECRET`; `ALLOWED_ORIGINS=https://poker-dev.noctifer20.com`; Dokploy's own autoDeploy off (GitHub Actions triggers it).
- **Edge:** the same shape as production — proxied record, Full (strict) via configuration rule, origin certificate for the hostname, Authenticated Origin Pulls, Traefik Cloudflare allow-list + proxy-secret header.
- **Who does what:** built end to end on 2026-09-29 by the main session on the owner's instruction (manual permission mode): repo, GitHub variable, Cloudflare, Dokploy, host timer. Kill switch: repo variable `DEV_DEPLOY_ENABLED`. Infra ids live in the owner's org vault, not here. No credentials in either repo or the vault.

## consequences
- The owner can look at a change on a real phone, through Cloudflare, before testers see it. Merging into `dev` restarts the dev server and voids dev hands — fine, it's a review box.
- Dev is **public** like production: S1/S2 (see [[status]]) apply to it too, and it shares the VPS's disk, memory and CPU with production. A runaway dev build or dev history file can hurt production.
- Log rotation set by hand is wiped on every redeploy; with auto-deploys that is now often, so rotation has to move to where it survives (Docker daemon defaults) — **assumption:** daemon `log-opts` apply to Swarm service tasks; unverified.
- New on the host: a root timer holding a Dokploy API key and a read-only GitHub deploy key. Nothing new faces the internet. `security-reviewer` hasn't reviewed the dev setup yet.
- From merge to live takes roughly CI (~1 min) + up to 1 min + the Docker build. The timer's log-rotation step restarts the dev container once more right after each deploy (a few seconds of 502).
- A flaky server test (`socket-server.test.ts`, "stale-hand") can turn a run red and hold a deploy back; rerun the failed job until it's fixed.
- ADR 0005's D1 stands for `main`. The later ADR recording production's deployed shape (listed in [[status]] next actions) now takes number 0007.

## revisit when
- Two changes need review at once and step on each other on `dev` → per-branch preview environments.
- Dev load or a dev incident affects production on the shared VPS.
- The VPS-poll option (B3) becomes preferable, e.g. if exposing the Dokploy API proves troublesome.
