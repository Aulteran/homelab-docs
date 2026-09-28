# Reverse proxy — Nginx Proxy Manager

NPM receives incoming HTTPS and routes it to internal services. It's also the front door for internal-only hostnames via [AdGuard rewrites](dns-adguard.md).

| | |
|---|---|
| **Host** | `PVE-7050` → **CT 101** (`npm`) |
| **LAN IP** | `10.10.0.101` *(inferred from CTID — confirm)* |
| **Domain URL** | [nginx.andrims.net](https://nginx.andrims.net) |
| **Admin UI (LAN)** | ❓ `http://10.10.0.101:81` |
| **Login** | (Vaultwarden → "Nginx Proxy Manager") ❓ entry name |
| **Certificates** | ❓ Let's Encrypt — HTTP challenge or Cloudflare DNS challenge? |

## Proxy hosts

| Domain | Forward to | Public? | SSL | Status |
|---|---|---|---|---|
| [stream.andrims.net](https://stream.andrims.net) | Jellyfin on Server-GF65 — `10.10.0.140:8096` | **Yes** | ❓ | ✅ |
| [immich.andrims.net](https://immich.andrims.net) | Immich — `10.10.0.107:2283` | **Yes** | ❓ | 🟡 planned (currently served by Cloudflare Tunnel, not NPM) |
| [vault.andrims.net](https://vault.andrims.net) | Vaultwarden on Server-GF65 — `10.10.0.140:❓` | No ❓ | ❓ | ✅ |
| [truenas.andrims.net](https://truenas.andrims.net) | TrueNAS — `10.10.0.22` | No ❓ | ❓ | ✅ |
| [paperless.andrims.net](https://paperless.andrims.net) | Paperless-ngx — `10.10.0.105` *(inferred)* :8000 | No ❓ | ❓ | ✅ |
| [servarr.andrims.net](https://servarr.andrims.net) | qBittorrent on Server-GF65 — `10.10.0.140:8080` ❓ (root); custom locations `/radarr` → `:7878`, `/sonarr` → `:8989`, `/prowlarr` → `:9696`, `/bazarr` → `:6767` ❓ | No ❓ | ❓ | ✅ |
| [dash.andrims.net](https://dash.andrims.net) | Glance on Monitoring Pi — ❓ IP:8080 | No ❓ | ❓ | ✅ |
| [nginx.andrims.net](https://nginx.andrims.net) | NPM admin — `10.10.0.101` *(inferred)* :81 | No ❓ | ❓ | ✅ |
| [dns.andrims.net](https://dns.andrims.net) | AdGuard Home admin — `10.10.0.53` | No ❓ | ❓ | ✅ |
| [pve-7050.andrims.net](https://pve-7050.andrims.net) | Proxmox — `10.10.0.15:8006` (HTTPS backend) | No ❓ | ❓ | ✅ |
| [dozzle.andrims.net](https://dozzle.andrims.net) | Dozzle on Monitoring Pi — `10.10.0.6:8080` ❓ port | No ❓ | ❓ | ✅ |
| [uptime.andrims.net](https://uptime.andrims.net) | Uptime Kuma on Monitoring Pi — ❓ IP:3001 | No ❓ | ❓ | ❓ |
| `git.andrims.net` | Forgejo — `10.10.0.106`:3000 ❓ | No | ❓ | ✅ |
| `docs.andrims.net` | docs LXC — ❓ IP:80 | No | ❓ | 🟡 |

## Gotchas

- ❓ Access lists used to restrict internal hosts?
- ❓ Websockets enabled for Jellyfin / Vaultwarden / Proxmox (the Proxmox console needs them)?
- `pve-7050.andrims.net` must forward with scheme **https** to port 8006 — Proxmox only serves HTTPS.
