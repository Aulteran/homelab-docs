# DNS — AdGuard Home

AdGuard Home provides custom DNS (ad-blocking + local rewrites) for the lab.

| | |
|---|---|
| **Host** | ❓ (LXC? which CTID?) |
| **IP** | ❓ |
| **Admin UI** | ❓ |
| **Upstream DNS** | ❓ |
| **Deployed via** | ❓ |
| **Backup / secondary DNS** | ❓ (if AdGuard dies, does the LAN lose DNS?) |

## DNS rewrites

Internal-only hostnames point at Nginx Proxy Manager, which then routes to the service.

| Hostname | Rewrites to | Status |
|---|---|---|
| `git.andrims.net` | NPM IP ❓ | 🟡 planned (Forgejo) |
| `docs.andrims.net` | NPM IP ❓ | 🟡 planned (docs site) |
| ❓ others | | |

!!! tip
    Internal names get rewrites here and **no** public Cloudflare record. That keeps them off the internet while still getting NPM's HTTPS.
