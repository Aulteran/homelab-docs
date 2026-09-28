# DNS — AdGuard Home

AdGuard Home provides custom DNS (ad-blocking + local rewrites) for the lab.

| | |
|---|---|
| **Host** | `PVE-7050` → **CT 153** (`adguard`) |
| **LAN IP** | `10.10.0.53` — note: **doesn't** follow the CTID-suffix convention |
| **Domain URL** | [dns.andrims.net](https://dns.andrims.net) |
| **Admin UI (LAN)** | ❓ `http://10.10.0.53` |
| **Upstream DNS** | ❓ |
| **Deployed via** | ❓ |
| **Backup / secondary DNS** | ❓ (if AdGuard dies, does the LAN lose DNS?) |

## DNS rewrites

Internal-only hostnames point at Nginx Proxy Manager, which then routes to the service.

| Hostname | Rewrites to | Status |
|---|---|---|
| `vault.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `truenas.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `paperless.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `servarr.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `dash.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `nginx.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `dns.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `pve-7050.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `dozzle.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `uptime.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `git.andrims.net` | `10.10.0.101` *(inferred — confirm)* | ✅ |
| `docs.andrims.net` | `10.10.0.101` *(inferred — confirm)* | 🟡 planned (docs site) |
| ❓ others (or one wildcard `*.andrims.net` rewrite?) | | |

!!! tip
    Internal names get rewrites here and **no** public Cloudflare record. That keeps them off the internet while still getting NPM's HTTPS.
