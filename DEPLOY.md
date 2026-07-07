# DEPLOY — alpha.sunified.ai

Operational record of how this site is hosted, how DNS is wired, and how changes reach production. Last updated 2026-07-07.

## Where it runs
Production is served by **Vercel** (account `lg-4884`, project `sunified-alpha`) over HTTPS at **https://alpha.sunified.ai**.

This repo still contains a GitHub Actions Pages workflow from an earlier attempt. **GitHub Pages is not the live host** — Vercel serves production. The Pages workflow can be ignored or removed.

## DNS (GoDaddy, zone sunified.ai)

| Purpose | Host | Type | Value |
|---|---|---|---|
| Routing | `alpha` | CNAME | `cname.vercel-dns.com` |
| Ownership verify | `_vercel` | TXT | `vc-domain-verify=alpha.sunified.ai,477db4c2374d0cb95e9f` |

The apex record `_vercel.sunified.ai` holds one TXT value per Vercel-hosted subdomain (alpha, quant, lgv). When editing, do not delete the sibling values. These tokens are already public in DNS and are not secrets.

## Attaching a subdomain to Vercel (runbook)
Because sunified.ai is not registered inside the Vercel account, each subdomain needs an ownership check:
1. Add the domain to the project via API: `POST https://api.vercel.com/v10/projects/<project>/domains` with body `{"name":"<sub>.sunified.ai"}`. The response returns the exact `_vercel` TXT to add.
2. Add that TXT at GoDaddy on host `_vercel` (leave existing values in place).
3. Verify: `POST .../domains/<sub>.sunified.ai/verify` returns `verified:true`.
4. Add CNAME `<sub>` to `cname.vercel-dns.com`. Vercel issues the TLS certificate automatically within a few minutes.

Note: the `vercel domains add` CLI returns a bare "Not authorized (403)" and hides the token. Use the REST API above.

## How changes reach production (current state)
Deploys are run from a local working copy with the Vercel CLI: `vercel deploy --prod` from this project directory. **Pushing to this GitHub repo does NOT auto-deploy** — the Vercel project is not yet Git-connected.

**Recommended target:** connect the Vercel project to this repo (Vercel dashboard, Project, Settings, Git). Then merges to `main` auto-deploy, and the team workflow becomes: branch, PR, Hicham (CTO) merges, Vercel deploys.

## Access and editing
- Org: `Sunified-Technologies-BV`. Write access via the `alpha-editors` team. Admins: FUSED-ID, x409.
- Copy edits go to the shared Copy Deck sheet. Design or code edits go via branch and PR, merged by Hicham.
