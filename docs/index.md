# Aadil's Homelab

A Proxmox-based homelab with a Windows Docker host, a dedicated monitoring Pi, and a UniFi network. Public DNS is on Cloudflare (`andrims.net`); two services are public (Jellyfin and Immich); everything else is internal, with remote access over Tailscale.

**Status legend:** ✅ running · 🟡 planned / in progress · ❓ unknown or unconfirmed

## At a glance

| Machine | Hostname | LAN IP | Role | Runs |
|---|---|---|---|---|
| [Dell OptiPlex 7050 SFF](hardware/optiplex-7050.md) | `PVE-7050` | `10.10.0.15` | Proxmox VE host | TrueNAS VM, Immich VM, Docker-in-LXC containers (CT 105, CT 108), more LXCs |
| [MSI GF65](hardware/msi-gf65.md) | `Server-GF65` | `10.10.0.140` | Windows 10 Pro + Docker Desktop | Jellyfin, Servarr stack (Radarr, Sonarr, Prowlarr, Bazarr, qBittorrent), Vaultwarden |
| [Monitoring Pi](hardware/monitoring-pi.md) | ❓ | ❓ | Raspberry Pi, monitoring only | Glance dashboard |
| [MSI Raider GE68 HX](hardware/msi-raider-ge68hx.md) | ❓ | DHCP | Main personal workstation (Windows 11) | Nothing — client |
| [M1 MacBook Air](hardware/macbook-air.md) | ❓ | DHCP | Secondary personal computer | Nothing — client |

## Services

Domain and direct LAN links for every service are on [Services → Quick links](services/index.md#quick-links).

| Service | Status | Host | Exposure |
|---|---|---|---|
| [Vaultwarden](services/vaultwarden.md) | ✅ | Server-GF65 (Docker Desktop) | Internal — [vault.andrims.net](https://vault.andrims.net) |
| [Immich](services/immich.md) | ✅ | VM ❓ on PVE-7050 | **Public** — [immich.andrims.net](https://immich.andrims.net) (Cloudflare Tunnel) |
| [TrueNAS](services/truenas.md) | ✅ | VM ❓ on PVE-7050 | Internal — [truenas.andrims.net](https://truenas.andrims.net) |
| [Paperless-ngx](services/paperless-ngx.md) | ✅ | ❓ | Internal — [paperless.andrims.net](https://paperless.andrims.net) |
| [Jellyfin](services/jellyfin.md) | ✅ | Server-GF65 | **Public** — [stream.andrims.net](https://stream.andrims.net) |
| [Servarr stack](services/servarr.md) | ✅ | Server-GF65 (Docker Desktop) | Internal — [servarr.andrims.net](https://servarr.andrims.net) (+ per-app paths) |
| [Nginx Proxy Manager](network/reverse-proxy.md) | ✅ | ❓ | Receives external HTTPS — admin at [nginx.andrims.net](https://nginx.andrims.net) |
| [AdGuard Home](network/dns-adguard.md) | ✅ | ❓ | Internal DNS — admin at [dns.andrims.net](https://dns.andrims.net) |
| [Glance](services/glance.md) | ✅ | Monitoring Pi | Internal — [dash.andrims.net](https://dash.andrims.net) |
| [Dozzle](services/dozzle.md) | 🟡 | Monitoring Pi + agents | Internal — [dozzle.andrims.net](https://dozzle.andrims.net) |
| [Uptime Kuma](services/uptime-kuma.md) | 🟡 | Monitoring Pi | Internal — [uptime.andrims.net](https://uptime.andrims.net) |
| [Forgejo](services/forgejo.md) | 🟡 | CT (TBD) on PVE-7050 | Internal — [git.andrims.net](https://git.andrims.net) |
| [Docs site](services/docs-site.md) | 🟡 | CT (TBD) on PVE-7050 | Internal — [docs.andrims.net](https://docs.andrims.net) |

## Network map

```mermaid
flowchart TB
    internet((Internet))
    cf["Cloudflare DNS<br/>andrims.net"]
    ts["Tailscale tailnet<br/>(remote access)"]

    internet -->|"stream.andrims.net<br/>DNS-only → home IP"| cf
    internet -->|"immich.andrims.net<br/>proxied"| cf
    cf --> unifi["UniFi gateway"]
    cf ==>|"Cloudflare Tunnel"| cfd["cloudflared ❓host"]
    ts -.-> unifi

    unifi --> npm["Nginx Proxy Manager ❓host"]
    unifi --> adguard["AdGuard Home ❓host"]

    subgraph pve["PVE-7050 (10.10.0.15) — Dell OptiPlex 7050 SFF, Proxmox VE"]
        truenas["VM: TrueNAS<br/>900 GB photo/media library"]
        immich["VM: Immich"]
        ct105["CT 105: Docker"]
        ct108["CT 108: Docker"]
        forgejo["LXC: Forgejo 🟡"]
        docs["LXC: docs (nginx) 🟡"]
    end

    subgraph gf65["Server-GF65 (10.10.0.140) — MSI GF65, Win 10 Pro + Docker Desktop"]
        jellyfin["Jellyfin"]
        arr["Radarr · Sonarr · Prowlarr<br/>Bazarr · qBittorrent"]
        vw["Vaultwarden"]
    end

    subgraph pi["Monitoring Pi"]
        glance["Glance"]
        dozzle["Dozzle 🟡"]
        kuma["Uptime Kuma 🟡"]
    end

    npm -->|stream.andrims.net| jellyfin
    npm -.->|"git.andrims.net (internal)"| forgejo
    npm -.->|"docs.andrims.net (internal)"| docs
    cfd ==>|immich.andrims.net| immich
    immich --> truenas
    dozzle -.->|agent| gf65
    dozzle -.->|agents| pve
```

## Where to start

- Filling gaps: [Open questions](open-questions.md) lists every ❓ in one place.
- What's next and the standing rules: [Roadmap & rules](roadmap.md).
- Adding a new service: copy [the service template](templates/service.md).
