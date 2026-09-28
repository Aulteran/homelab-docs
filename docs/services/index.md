# Services

New services start from the [service template](../templates/service.md).

## Quick links

Every service gets two links: the **domain** (the normal way in) and the **LAN URL** (straight to the host's LAN IP and port, skipping DNS and the proxy).

| Service | Status | Host | Domain | LAN (direct) | Public? | Critical? |
|---|---|---|---|---|---|---|
| [Vaultwarden](vaultwarden.md) | ✅ | Server-GF65 (Docker Desktop) | [vault.andrims.net](https://vault.andrims.net) | ❓ `http://10.10.0.140:<port>` | ❓ | **Yes** |
| [Immich](immich.md) | ✅ | PVE-7050 → VM 107 | [immich.andrims.net](https://immich.andrims.net) (Cloudflare Tunnel, not NPM) | ❓ `http://<IP>:2283` | **Yes** | **Yes** |
| [TrueNAS](truenas.md) | ✅ | PVE-7050 → VM 104 | [truenas.andrims.net](https://truenas.andrims.net) | ❓ `http://<IP>` | No | **Yes** |
| [Paperless-ngx](paperless-ngx.md) | ✅ | PVE-7050 → CT 105 | [paperless.andrims.net](https://paperless.andrims.net) | ❓ `http://<IP>:8000` | ❓ | **Yes** |
| [Jellyfin](jellyfin.md) | ✅ | Server-GF65 | [stream.andrims.net](https://stream.andrims.net) | [http://10.10.0.140:8096](http://10.10.0.140:8096) | **Yes** | No |
| Radarr ([Servarr](servarr.md)) | ✅ | Server-GF65 (Docker Desktop) | [servarr.andrims.net/radarr](https://servarr.andrims.net/radarr) | [http://10.10.0.140:7878](http://10.10.0.140:7878) | ❓ | No |
| Sonarr ([Servarr](servarr.md)) | ✅ | Server-GF65 (Docker Desktop) | [servarr.andrims.net/sonarr](https://servarr.andrims.net/sonarr) | [http://10.10.0.140:8989](http://10.10.0.140:8989) | ❓ | No |
| Prowlarr ([Servarr](servarr.md)) | ✅ | Server-GF65 (Docker Desktop) | [servarr.andrims.net/prowlarr](https://servarr.andrims.net/prowlarr) | [http://10.10.0.140:9696](http://10.10.0.140:9696) | ❓ | No |
| Bazarr ([Servarr](servarr.md)) | ✅ | Server-GF65 (Docker Desktop) | [servarr.andrims.net/bazarr](https://servarr.andrims.net/bazarr) | [http://10.10.0.140:6767](http://10.10.0.140:6767) | ❓ | No |
| qBittorrent ([Servarr](servarr.md)) | ✅ | Server-GF65 (Docker Desktop) ❓ | [servarr.andrims.net](https://servarr.andrims.net) | ❓ `http://10.10.0.140:8080` | ❓ | No |
| [Glance](glance.md) | ✅ | Monitoring Pi | [dash.andrims.net](https://dash.andrims.net) | ❓ `http://<IP>:8080` | ❓ | No |
| [Nginx Proxy Manager](../network/reverse-proxy.md) (admin) | ✅ | PVE-7050 → CT 101 | [nginx.andrims.net](https://nginx.andrims.net) | ❓ `http://<IP>:81` | No | **Yes** |
| [AdGuard Home](../network/dns-adguard.md) (admin) | ✅ | PVE-7050 → CT 153 | [dns.andrims.net](https://dns.andrims.net) | ❓ `http://<IP>` | No | **Yes** |
| [Proxmox VE](../proxmox/host.md) (admin) | ✅ | PVE-7050 (bare metal) | [pve-7050.andrims.net](https://pve-7050.andrims.net) | [https://10.10.0.15:8006](https://10.10.0.15:8006) | No | **Yes** |
| [Dozzle](dozzle.md) | ✅ | Monitoring Pi + agents | [dozzle.andrims.net](https://dozzle.andrims.net) | — | No | No |
| [Uptime Kuma](uptime-kuma.md) | 🟡 | Monitoring Pi | [uptime.andrims.net](https://uptime.andrims.net) | — | No | No |
| [Forgejo](forgejo.md) | ✅ | PVE-7050 → CT 106 | [git.andrims.net](https://git.andrims.net) | ❓ `http://10.10.0.106:3000` | No | No |
| [ActualBudget](actualbudget.md) | ✅ | PVE-7050 → CT 110 | ❓ | ❓ `http://10.10.0.110:5006` | No | **Yes** |
| [Docs site](docs-site.md) | 🟡 | PVE-7050 → CT (TBD) | [docs.andrims.net](https://docs.andrims.net) | — | No | No |

**Host** names the physical machine and, for Proxmox guests, the specific VM or CT (e.g. `PVE-7050 → CT 105`). Guest IDs are listed in the [IP / CTID table](../proxmox/ip-ctid-table.md).

LAN links for Server-GF65 use each app's **default port** — confirm them if you changed any. The *arr apps sit under paths on `servarr.andrims.net`, so each has a **URL Base** set (`/radarr`, `/sonarr`, …) — if a LAN link lands on a blank page or 404, add that path to the end (e.g. `http://10.10.0.140:7878/radarr`). Fill in the ❓ ones as `[http://10.10.0.x:port](http://10.10.0.x:port)`.

"Critical" = losing its data would hurt. These need a tested backup and restore runbook first.

## Troubleshooting with the two links

| Domain works? | LAN URL works? | Where the problem is |
|---|---|---|
| ✅ | ✅ | Nothing (or it's the client) |
| ❌ | ✅ | The path in front of the service: [NPM](../network/reverse-proxy.md), [AdGuard rewrite](../network/dns-adguard.md), [Cloudflare](../network/cloudflare.md) DNS/tunnel, certificate, or port forward |
| ❌ | ❌ | The service or its host: container stopped, VM/LXC down, host asleep, firewall |
| ✅ | ❌ | Wrong LAN IP/port in these docs — the IP probably changed |
