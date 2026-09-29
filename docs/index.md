# Aadil's Homelab

A Proxmox-based homelab with a Windows Docker host, a dedicated monitoring Pi, and a UniFi network. Public DNS is on Cloudflare (`andrims.net`); four services are public (Jellyfin, Seerr, Immich and Vaultwarden); everything else is internal, with remote access over Tailscale.

**Status legend:** ✅ running · 🟡 planned / in progress · ❓ unknown or unconfirmed

## At a glance

| Machine | Hostname | LAN IP | Role | Runs |
|---|---|---|---|---|
| [Dell OptiPlex 7050 SFF](hardware/optiplex-7050.md) | `PVE-7050` | `10.10.0.15` | Proxmox VE host | TrueNAS (VM 104), Immich (VM 107), NPM (CT 101), cloudflared (CT 102), Docker host (CT 105: docs site, Paperless-ngx), Forgejo (CT 106), ddns-updater (CT 109), ActualBudget (CT 110), AdGuard (CT 153); Home Assistant OS (VM 171, stopped) |
| [MSI GF65](hardware/msi-gf65.md) | `Server-GF65` | `10.10.0.140` | Windows 10 Pro + Docker Desktop | Jellyfin, Seerr, Servarr stack (Radarr, Sonarr, Lidarr, Prowlarr, Bazarr, qBittorrent, Gluetun, Tdarr, Jellystat), Vaultwarden |
| [Monitoring Pi](hardware/monitoring-pi.md) | `raspberrypi` | `10.10.0.6` | Raspberry Pi 5, monitoring only | Glance dashboard, Dozzle, Uptime Kuma (Homepage being decommissioned) |
| [MSI Raider GE68 HX](hardware/msi-raider-ge68hx.md) | — (client) | DHCP | Main personal workstation (Windows 11) | Nothing — client |
| [M1 MacBook Air](hardware/macbook-air.md) | — (client) | DHCP | Secondary personal computer | Nothing — client |

## Services

Domain and direct LAN links for every service are on [Services → Quick links](services/index.md#quick-links).

| Service | Status | Host | Exposure |
|---|---|---|---|
| [Vaultwarden](services/vaultwarden.md) | ✅ | Server-GF65 (Docker Desktop) | **Public** — [vault.andrims.net](https://vault.andrims.net) (Cloudflare Tunnel) |
| [Immich](services/immich.md) | ✅ | VM 107 on PVE-7050 | **Public** — [immich.andrims.net](https://immich.andrims.net) (Cloudflare Tunnel) |
| [TrueNAS](services/truenas.md) | ✅ | VM 104 on PVE-7050 | Internal — [truenas.andrims.net](https://truenas.andrims.net) |
| [Paperless-ngx](services/paperless-ngx.md) | ✅ | CT 105 (`docker`) on PVE-7050 | Internal — [paperless.andrims.net](https://paperless.andrims.net) |
| [Jellyfin](services/jellyfin.md) | ✅ | Server-GF65 | **Public** — [stream.andrims.net](https://stream.andrims.net) |
| Seerr (formerly Jellyseerr) | ✅ | Server-GF65 | **Public** — [request.andrims.net](https://request.andrims.net) (Cloudflare Tunnel) |
| [Servarr stack](services/servarr.md) | ✅ | Server-GF65 (Docker Desktop) | Internal — [servarr.andrims.net](https://servarr.andrims.net) (+ per-app paths) |
| [Nginx Proxy Manager](network/reverse-proxy.md) | ✅ | CT 101 on PVE-7050 | Receives external HTTPS — admin at [nginx.andrims.net](https://nginx.andrims.net) |
| [AdGuard Home](network/dns-adguard.md) | ✅ | CT 153 on PVE-7050 | Internal DNS — admin at [dns.andrims.net](https://dns.andrims.net) |
| [Glance](services/glance.md) | ✅ | Monitoring Pi | Internal — [dash.andrims.net](https://dash.andrims.net) |
| [Dozzle](services/dozzle.md) | ✅ | Monitoring Pi + agents | Internal — [dozzle.andrims.net](https://dozzle.andrims.net) |
| [Uptime Kuma](services/uptime-kuma.md) | ✅ | Monitoring Pi | Internal — [uptime.andrims.net](https://uptime.andrims.net) |
| [Forgejo](services/forgejo.md) | ✅ | CT 106 on PVE-7050 | Internal — [git.andrims.net](https://git.andrims.net) |
| [ActualBudget](services/actualbudget.md) | ✅ | CT 110 on PVE-7050 | Internal — [budget.andrims.net](https://budget.andrims.net) |
| [Docs site](services/docs-site.md) | ✅ | CT 105 (`docker`) on PVE-7050 — auto-rebuild on push 🟡 | Internal — [docs.andrims.net](https://docs.andrims.net) |

## Network map

```mermaid
flowchart TB
    internet((Internet))
    cf["Cloudflare DNS<br/>andrims.net"]
    ts["Tailscale tailnet<br/>(remote access)"]

    internet -->|"stream.andrims.net<br/>DNS-only → home IP"| cf
    internet -->|"immich.andrims.net<br/>proxied"| cf
    internet -->|"vault.andrims.net<br/>proxied"| cf
    internet -->|"request.andrims.net<br/>proxied"| cf
    cf --> unifi["UniFi UX7<br/>gateway"]
    ts -.-> unifi

    subgraph pve["PVE-7050 (10.10.0.15) — Dell OptiPlex 7050 SFF, Proxmox VE"]
        npm["CT 101: NPM"]
        cfd["CT 102: cloudflared"]
        truenas["VM 104: TrueNAS<br/>900 GB photo/media library"]
        forgejo["CT 106: Forgejo"]
        immich["VM 107: Immich"]
        ddns["CT 109: ddns-updater"]
        budget["CT 110: ActualBudget"]
        adguard["CT 153: AdGuard Home"]
        haos["VM 171: Home Assistant OS<br/>⏸️ stopped"]
        subgraph dockerct["CT 105: docker"]
            docs["docs-site nginx"]
            paperless["Paperless-ngx"]
        end
    end

    subgraph gf65["Server-GF65 (10.10.0.140) — MSI GF65, Win 10 Pro + Docker Desktop"]
        jellyfin["Jellyfin"]
        seerr["Seerr"]
        arr["Radarr · Sonarr · Prowlarr<br/>Bazarr · qBittorrent"]
        vw["Vaultwarden"]
    end

    subgraph pi["Monitoring Pi (10.10.0.6)"]
        glance["Glance"]
        dozzle["Dozzle"]
        kuma["Uptime Kuma"]
    end

    unifi --> npm
    unifi -.->|LAN DNS| adguard
    cf ==>|"Cloudflare Tunnel"| cfd
    cfd ==>|immich.andrims.net| immich
    cfd ==>|vault.andrims.net| vw
    cfd ==>|request.andrims.net| seerr
    ddns -.->|"keeps aultmain.andrims.net<br/>pointed at home IP"| cf
    npm -->|stream.andrims.net| jellyfin
    npm -.->|"git.andrims.net (internal)"| forgejo
    npm -.->|"docs.andrims.net (internal)"| docs
    forgejo -.->|"push webhook → rebuild 🟡"| docs
    immich --> truenas
    dozzle -.->|agent| gf65
    dozzle -.->|agents| pve
```

## Where to start

- Filling gaps: [Open questions](open-questions.md) lists every ❓ in one place.
- What's next and the standing rules: [Roadmap & rules](roadmap.md).
- Adding a new service: copy [the service template](templates/service.md).
