# MSI GF65 — Windows Docker host

A laptop repurposed as a server. Runs **Windows 10 Pro** with **Docker Desktop**.

!!! danger "Broken screen, no BIOS access: Linux can't be installed on this machine"
    The laptop's screen and speakers have not worked since **2022**, when it was opened to clean off and redo the thermal paste. Something on the motherboard most likely shorted then (static wasn't discharged first). The screen panel itself is probably fine.

    The BIOS/UEFI setup **doesn't show on an external monitor** either, so there is no way to get into it to disable Secure Boot or change the boot order. **Windows is therefore permanent on this machine, and it can't be moved to Linux or Proxmox.** It is managed entirely over RDP. The way out is to replace it (see the [roadmap](../roadmap.md#planned)).

| | |
|---|---|
| **CPU** | Intel Core i7-9750H |
| **RAM** | 16 GB DDR4 |
| **GPU** | NVIDIA GTX 1660 Ti (laptop) |
| **Boot disk** | 256 GB SSD, the only SSD. **Nearly always full.** Windows and Jellyfin's install (and its config/metadata) are on it. |
| **External storage** | Two USB hard disks, see below |
| **Always on?** | Yes |
| **LAN IP** | `10.10.0.140` |
| **Hostname** | `Server-GF65` |
| **Tailscale** | Installed, IP `100.69.160.6`; acts as a tailnet **exit node** (rarely used). See [Tailscale](../network/tailscale.md). |

## What runs here

- [Jellyfin](../services/jellyfin.md) — public at [stream.andrims.net](https://stream.andrims.net). Installed **bare metal** on Windows (not in Docker), on the boot SSD. Transcoding happens on this machine with **NVENC hardware acceleration** on the GTX 1660 Ti.
- [Servarr stack](../services/servarr.md) — one Docker Compose project (`servarr`, 11 containers): Radarr, Sonarr, Lidarr, Prowlarr, Bazarr, qBittorrent, Gluetun, Tdarr, Seerr, Jellystat and its database (Docker Desktop)
- Seerr (formerly Jellyseerr) — media requests, public at [request.andrims.net](https://request.andrims.net) via Cloudflare Tunnel (part of the `servarr` project)
- [Vaultwarden](../services/vaultwarden.md) (Docker Desktop) — public via Cloudflare Tunnel
- `dozzle-agent` (Docker Desktop) — sends this host's container logs to [Dozzle](../services/dozzle.md) on the Monitoring Pi

That is 13 containers in all, plus Jellyfin outside Docker. **Jellystat has been down for a few days** (not yet diagnosed).

## External drives

| Name | Drive | Size | What's on it |
|---|---|---|---|
| **Neo** | Seagate Backup Plus Ultra Touch, USB HDD | 2 TB | All of [Jellyfin's](../services/jellyfin.md) media |
| **Curiosity** | Seagate Backup Plus Portable, USB HDD | 5 TB | Long-term storage: old personal files, family photos and videos, old video projects. Rarely accessed. **No backup.** Family photos and videos are also in Immich. |

## Known risks

!!! warning "Windows 10 is past end of support"
    Windows 10 stopped getting security updates in October 2025. This machine hosts **Vaultwarden**, which makes that matter more than it would for a media box.

- **The 256 GB boot SSD is nearly always full.** Jellyfin's install and metadata live on it, and so does **Docker Desktop's data** (the Vaultwarden and Servarr containers and volumes). A full boot disk can break any of them. Moving Jellyfin's data folder and Docker's disk location to Neo (or adding a bigger SSD) would free room.
- **Media and personal archives are on single USB hard disks.** Jellyfin's media can be re-acquired, but **Curiosity has no copy anywhere.** Only the family photos and videos were also put into [Immich](../services/immich.md), so those live on the TrueNAS SATA SSD too (a single disk with no redundancy or off-box backup, so that is a second copy, not a backup). Everything else on Curiosity, including the old video projects and other files, exists only on that one drive.
- **No screen and no BIOS access.** If Windows fails to boot or RDP stops working, there is no console to fix it from. Nothing can be changed in BIOS, including the "power on after power loss" setting. ❓ Does the laptop power back on by itself after a power cut? ❓ Does Windows itself display on an external monitor?
- **Docker Desktop doesn't start until a user logs in.** Auto-login is **not** set up. After a restart or power cut, the Docker services (Vaultwarden, Seerr, Servarr) stay down until Aadil **logs in manually over RDP**. Vaultwarden is public, so its outage is visible from outside.
- **WSL2 updates can break Docker Desktop.**
- **This box can't be moved to Linux or Proxmox** (see the warning at the top). The plan is to replace it with a purpose-built server: better Jellyfin performance and proper hard drives in RAID. Until then, keep Vaultwarden's data backed up somewhere off this laptop. See [Roadmap](../roadmap.md).

## Monitoring

- ✅ A [Dozzle](../services/dozzle.md) agent (`dozzle-agent`) is running here and visible in the main Dozzle on the Monitoring Pi. ❓ Does it use port 7007 over the LAN or over Tailscale?
