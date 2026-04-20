# Agent Readiness — Brother Handyman Services

Audited 2026-04-20 against https://brothers-handyman-production.up.railway.app/.

**Starting score: 1/14 checks passing.**
**Projected score after deploying this folder: 7/14** (plus 1 more if you drop in the Caddyfile).

The rest (MCP, OAuth, API catalog) are intentionally skipped — they only matter for sites with agent-drivable actions (checkout, search, auth). This is a brochure site.

## What's in this folder

| File | Goes to | Why |
|---|---|---|
| `robots.txt` | repo root → `/robots.txt` | AI bot rules + Content Signals (`ai-train=no`, `ai-input=yes`) |
| `llms.txt` | repo root → `/llms.txt` | Structured site summary for LLM agents |
| `sitemap.xml` | repo root → `/sitemap.xml` | Standard XML sitemap (one URL — it's a single-page site) |
| `Caddyfile` | repo root (optional) | Adds `Link:` response header so the three files are discoverable via header, not just path guessing |

## How to deploy

1. Copy `robots.txt`, `llms.txt`, and `sitemap.xml` into the root of the `Brothers Handyman` repo (same folder as `index.html`).
2. Optional: copy `Caddyfile` too — Railway will auto-detect it and serve via Caddy with the `Link:` header. Skip if you don't want to change the serving layer.
3. Commit, push, Railway auto-deploys. Done.

## What was skipped and why

| Check | Why skipped |
|---|---|
| MCP Server Card | Site has no agent-drivable actions — no forms, no booking, no search. A stub would be noise. |
| Agent Skills index | Same reason. |
| OAuth AS / PR metadata | No authenticated APIs. |
| API catalog | No API. |
| Web Bot Auth | Advanced — adopt later if abusive traffic becomes a problem. |
| Markdown content negotiation | Not worth a custom server for a one-page brochure. `llms.txt` covers the same need. |

## Verification

After deploying, re-run the audit:

```bash
curl -s https://brothers-handyman-production.up.railway.app/robots.txt
curl -s https://brothers-handyman-production.up.railway.app/llms.txt
curl -s https://brothers-handyman-production.up.railway.app/sitemap.xml
curl -sI https://brothers-handyman-production.up.railway.app/ | grep -i '^link:'   # only if you deployed the Caddyfile
```

Or paste the URL into [isitagentready.com](https://isitagentready.com) for the official Cloudflare score.
