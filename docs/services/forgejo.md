# Forgejo (planned)

> **What / why:** Self-hosted git server. Source of truth for this docs repo, mirrored to a private GitHub repo as a backup.

**Status:** 🟡 planned

## Where

| | |
|---|---|
| **Host** | Proxmox — LXC via the [community helper script](https://community-scripts.github.io/ProxmoxVE/scripts?id=forgejo) |
| **CTID / IP** | ❓ (static IP — add to the [IP / CTID table](../proxmox/ip-ctid-table.md)) |
| **Resources** | **Advanced** install: 1 core, 512 MB RAM, 4–6 GB disk. Alpine profile optional. |
| **Database** | SQLite (script default) — don't add Postgres/MariaDB |

Expected footprint: ~100–200 MB RAM idle, near-zero CPU. The LXC limit is a ceiling, not a reservation.

## Access

| | |
|---|---|
| **URL** | `https://git.andrims.net` |
| **Exposure** | **Internal only.** AdGuard rewrite → NPM → Forgejo. No Cloudflare record. Remote via Tailscale. |
| **SSH clone** | Forgejo built-in SSH, or map port 2222 — ❓ which |

## GitHub backup (push mirror)

1. Create an empty **private** GitHub repo.
2. Create a fine-grained PAT scoped to that one repo, **Contents: read/write**. Store it in Vaultwarden.
3. Forgejo → repo **Settings → Mirror settings** → add a **push mirror** with the PAT. Turn on **sync when commits are pushed**.

## Setup checklist

- [ ] Deploy LXC, static IP
- [ ] AdGuard rewrite `git.andrims.net` → NPM
- [ ] NPM proxy host → Forgejo (port 3000)
- [ ] SSH clone access working from the Raider and the MacBook
- [ ] Create `homelab-docs` repo, push this repo
- [ ] Push mirror to private GitHub
- [ ] Set `repo_url` in `mkdocs.yml`
- [ ] Add to Glance; add Uptime Kuma check when that's up
- [ ] Update IP/CTID table + changelog
