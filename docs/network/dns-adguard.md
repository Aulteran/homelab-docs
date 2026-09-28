# DNS — AdGuard Home

AdGuard Home provides custom DNS (ad-blocking + local rewrites) for the lab.

| | |
|---|---|
| **Host** | ❓ (`PVE-7050` → which CT?) |
| **LAN IP** | ❓ |
| **Domain URL** | [dns.andrims.net](https://dns.andrims.net) |
| **Admin UI (LAN)** | ❓ `http://<LAN IP>` |
| **Upstream DNS** | ❓ |
| **Deployed via** | ❓ |
| **Backup / secondary DNS** | ❓ (if AdGuard dies, does the LAN lose DNS?) |

## DNS rewrites

Internal-only hostnames point at Nginx Proxy Manager, which then routes to the service.

| Hostname | Rewrites to | Status |
|---|---|---|
| `vault.andrims.net` | NPM IP ❓ | ✅ |
| `truenas.andrims.net` | NPM IP ❓ | ✅ |
| `paperless.andrims.net` | NPM IP ❓ | ✅ |
| `servarr.andrims.net` | NPM IP ❓ | ✅ |
| `dash.andrims.net` | NPM IP ❓ | ✅ |
| `nginx.andrims.net` | NPM IP ❓ | ✅ |
| `dns.andrims.net` | NPM IP ❓ | ✅ |
| `pve-7050.andrims.net` | NPM IP ❓ | ✅ |
| `dozzle.andrims.net` | NPM IP ❓ | ✅ |
| `uptime.andrims.net` | NPM IP ❓ | ✅ |
| `git.andrims.net` | NPM IP ❓ | 🟡 planned (Forgejo) |
| `docs.andrims.net` | NPM IP ❓ | 🟡 planned (docs site) |
| ❓ others (or one wildcard `*.andrims.net` rewrite?) | | |

!!! tip
    Internal names get rewrites here and **no** public Cloudflare record. That keeps them off the internet while still getting NPM's HTTPS.
