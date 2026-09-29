# MSI GF65 — Windows Docker host

A laptop repurposed as a server. Runs **Windows 10 Pro** with **Docker Desktop**.

| | |
|---|---|
| **CPU / RAM / GPU** | ❓ |
| **Storage** | ❓ (where does Jellyfin media live?) |
| **LAN IP** | `10.10.0.140` |
| **Hostname** | `Server-GF65` |
| **Tailscale** | Installed, IP `100.69.160.6`; acts as a tailnet **exit node** (rarely used). See [Tailscale](../network/tailscale.md). |

## What runs here

- [Jellyfin](../services/jellyfin.md) — public at [stream.andrims.net](https://stream.andrims.net). Transcoding happens on this machine (❓ GPU/hardware acceleration or software).
- [Servarr stack](../services/servarr.md) — Radarr, Sonarr, Prowlarr, Bazarr, qBittorrent (Docker Desktop)
- [Vaultwarden](../services/vaultwarden.md) (Docker Desktop) — public via Cloudflare Tunnel
- Seerr (formerly Jellyseerr) — media requests, public at [request.andrims.net](https://request.andrims.net) via Cloudflare Tunnel

## Known risks

!!! warning "Windows 10 is past end of support"
    Windows 10 stopped getting security updates in October 2025. This machine hosts **Vaultwarden**, which makes that matter more than it would for a media box.

- **Docker Desktop doesn't start until a user logs in.** After a reboot or power cut, nothing on this box comes back on its own until someone signs in. ❓ Is auto-login configured?
- **WSL2 updates can break Docker Desktop.**
- **Longer term:** move this box to Debian or Proxmox so it behaves like the rest of the lab. Until then, keep Vaultwarden's data backed up somewhere off this laptop. See [Roadmap](../roadmap.md).

## Monitoring

- Planned: a [Dozzle](../services/dozzle.md) agent on port 7007. Allow 7007 through Windows Firewall, or reach it over Tailscale instead of the LAN. Set this agent up first, since it's the host most likely to fight you (firewall, socket path).
