# DNS — AdGuard Home

AdGuard Home provides custom DNS (ad-blocking + local rewrites) for the lab.

| | |
|---|---|
| **Host** | `PVE-7050` → **CT 153** (`adguard-alpine`) — 1 core / 128 MB / 10 GB |
| **LAN IP** | `10.10.0.53` — note: **doesn't** follow the CTID-suffix convention |
| **Domain URL** | [dns.andrims.net](https://dns.andrims.net) |
| **Admin UI (LAN)** | [http://10.10.0.53](http://10.10.0.53) |
| **Upstream DNS** | `1.1.1.1` (primary) → `https://dns10.quad9.net:443/dns-query` (Quad9 DoH, backup) → `10.10.0.1` (third backup — likely the ISP/Xfinity router's own resolver) |
| **Deployed via** | Community helper script (Alpine variant), Alpine 3.23. Network is on `net1`, not `net0`. |
| **Backup / secondary DNS** | ❓ (if AdGuard dies, does the LAN lose DNS?) |
| **Query log** | Enabled, kept for **90 days**, written to disk. Statistics kept 7 days. |

!!! danger "The query log is filling the disk"
    On 2026-10-25 `/opt/AdGuardHome/data/querylog.json` (2.8 GB) plus `querylog.json.1` (4.5 GB) took **7.3 GB of the CT's 10 GB disk** (79% full). When it fills, AdGuard stops working and the LAN loses DNS. Fix: set the query log retention to 7 days or less (Settings → General settings → Query logs) or turn off writing it to disk. It also cuts constant writes to the boot NVMe. The CT also uses 96 of its 128 MB of RAM.

## DNS rewrites

Internal-only hostnames point at Nginx Proxy Manager, which then routes to the service.

| Hostname | Rewrites to | Status |
|---|---|---|
| `truenas.andrims.net` | `10.10.0.101` | ✅ |
| `paperless.andrims.net` | `10.10.0.101` | ✅ |
| `servarr.andrims.net` | `10.10.0.101` | ✅ |
| `dash.andrims.net` | `10.10.0.101` | ✅ |
| `nginx.andrims.net` | `10.10.0.101` | ✅ |
| `dns.andrims.net` | `10.10.0.101` | ✅ |
| `pve-7050.andrims.net` | `10.10.0.101` | ✅ |
| `dozzle.andrims.net` | `10.10.0.101` | ✅ |
| `uptime.andrims.net` | `10.10.0.101` | ✅ |
| `git.andrims.net` | `10.10.0.101` | ✅ |
| `docs.andrims.net` | `10.10.0.101` | ✅ |
| ❓ others (or one wildcard `*.andrims.net` rewrite?) | | |

!!! tip
    Internal names get rewrites here and **no** public Cloudflare record. That keeps them off the internet while still getting NPM's HTTPS.
