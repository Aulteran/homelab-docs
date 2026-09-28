# Docs site (planned)

> **What / why:** Renders this repo as a browsable site at `docs.andrims.net`.

**Status:** 🟡 planned

## Builder

Build with **Zensical** — it reads this `mkdocs.yml` as-is. Material for MkDocs is in maintenance mode and reaches end of life in **November 2026**; pinned `mkdocs-material` still works as a fallback with no changes.

## Phase 1 — simple

- Small **"docs" LXC** running nginx.
- Cron every 5 minutes: `git pull && zensical build`, serve the `site/` folder.
- NPM proxy host `docs.andrims.net`, **internal only** (AdGuard rewrite, no Cloudflare record).

## Phase 2 — later

- Replace cron with **Forgejo Actions**: a runner LXC builds on every push and deploys to the nginx container.

## Local preview

```bash
zensical serve     # or: mkdocs serve
```

## Setup checklist

- [ ] Forgejo up first
- [ ] docs LXC + nginx, static IP
- [ ] Cron build job
- [ ] AdGuard rewrite + NPM proxy host
- [ ] Update IP/CTID table + changelog
