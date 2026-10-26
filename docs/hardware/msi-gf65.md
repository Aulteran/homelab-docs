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
| **Boot disk** | 256 GB NVMe SSD (WD PC SN530), the only SSD. **Nearly always full**: 11.9 GB free of 237.8 GB on 2026-10-25. Docker Desktop's WSL data (`C:\Users\admin\AppData\Local\Docker\wsl`) takes **66 GB** and Jellyfin's data (`C:\ProgramData\Jellyfin`) **11.6 GB**. |
| **External storage** | Two USB hard disks plus a 57 GB SanDisk Ultra USB stick (`D:`, almost empty), see below |
| **OS** | Windows 10 Pro 22H2 (build 19045). No ESU licence found. Defender on, signatures current. Windows Firewall on for all profiles. |
| **Power plan** | High performance |
| **Always on?** | Yes |
| **LAN IP** | `10.10.0.140` |
| **Hostname** | `Server-GF65` |
| **SSH** | OpenSSH Server, user `admin`, key-only access for the `claude-homelab` key (`C:\ProgramData\ssh\administrators_authorized_keys`). Default shell is `cmd`. |
| **RDP** | On, with Network Level Authentication |
| **Tailscale** | Installed (1.104.2), IP `100.69.160.6`; acts as a tailnet **exit node** (rarely used). See [Tailscale](../network/tailscale.md). |

## What runs here

- [Jellyfin](../services/jellyfin.md) — public at [stream.andrims.net](https://stream.andrims.net). Installed **bare metal** on Windows (not in Docker), on the boot SSD. Transcoding happens on this machine with **NVENC hardware acceleration** on the GTX 1660 Ti.
- [Servarr stack](../services/servarr.md) — one Docker Compose project (`servarr`, 11 containers): Radarr, Sonarr, Lidarr, Prowlarr, Bazarr, qBittorrent, Gluetun, Tdarr, Seerr, Jellystat and its database (Docker Desktop)
- Seerr (formerly Jellyseerr) — media requests, public at [request.andrims.net](https://request.andrims.net) via Cloudflare Tunnel (part of the `servarr` project)
- [Vaultwarden](../services/vaultwarden.md) (Docker Desktop) — public via Cloudflare Tunnel
- `dozzle-agent` (Docker Desktop) — sends this host's container logs to [Dozzle](../services/dozzle.md) on the Monitoring Pi

That is 13 containers in all, plus Jellyfin outside Docker (`C:\Program Files\Jellyfin\Server\jellyfin.exe`, started by the tray app). All images use `:latest` except `jellystat-db` (`postgres:18.1`). Docker also holds **17 unused volumes (1.7 GB)** that no container uses — ❓ check what they are before pruning.

**Jellystat was fixed on 2026-10-23.** It had been crash-looping for about a month on migration `101_ji_insert_playback_plugin_data_to_activity_table.js` (`MERGE command cannot affect row a second time`), caused by 30 duplicate `EpisodeId` rows in `jf_library_episodes` (DB `jfstat`). The duplicates were deleted, keeping one row each, after a `pg_dump` to `C:\Users\admin\jfstat-pre-fix.dump`, which can be deleted once Jellystat is known to be fine. Watch history from the downtime is lost. **Risk:** the Jellyfin sync could create duplicates again, and the next Jellystat upgrade would break the same way.

## External drives

| Name | Drive | Size | What's on it |
|---|---|---|---|
| **Neo** (`G:`) | Seagate Backup Plus Ultra Touch, USB HDD | 2 TB (135 GB free on 2026-10-25) | All of [Jellyfin's](../services/jellyfin.md) media |
| **Curiosity** (`F:`) | Seagate Backup Plus Portable, USB HDD | 5 TB (1.47 TB free) | Long-term storage: old personal files, family photos and videos, old video projects. Rarely accessed. **No backup.** Family photos and videos are also in Immich. |

!!! danger "Curiosity's file system needs repair"
    Since the boot on 2026-09-27, Windows has reported Curiosity (`F:`) as **Health: Warning, "Full Repair Needed"**, and the volume is flagged dirty. The NTFS event log says it needs an offline `chkdsk /F`. The drive's own counters show **0 read errors** after 33,265 power-on hours, so this looks like file-system damage rather than a failing disk. It's still the only copy of that data: **copy off what matters before running `chkdsk /F`**, because a repair can move or drop damaged files. Windows also logged that two disks have the same disk identifier (event 158).

## Known risks

!!! warning "Windows 10 is past end of support"
    Windows 10 stopped getting security updates in October 2025. This machine hosts **Vaultwarden**, which makes that matter more than it would for a media box.

- **The 256 GB boot SSD is nearly always full** (11.9 GB free on 2026-10-25). Jellyfin's install and metadata (11.6 GB) live on it, and so does **Docker Desktop's data** (66 GB: the Vaultwarden and Servarr containers and volumes). A full boot disk can break any of them. Moving Jellyfin's data folder and Docker's disk location to Neo (or adding a bigger SSD) would free room, but Neo only has 135 GB free.
- **Media and personal archives are on single USB hard disks.** Jellyfin's media can be re-acquired, but **Curiosity has no copy anywhere.** Only the family photos and videos were also put into [Immich](../services/immich.md), so those live on the TrueNAS SATA SSD too (a single disk with no redundancy or off-box backup, so that is a second copy, not a backup). Everything else on Curiosity, including the old video projects and other files, exists only on that one drive.
- **No screen and no BIOS access.** If Windows fails to boot or RDP stops working, there is no console to fix it from. Nothing can be changed in BIOS, including the "power on after power loss" setting. ❓ Does the laptop power back on by itself after a power cut? ❓ Does Windows itself display on an external monitor?
- **Docker Desktop doesn't start until a user logs in.** Auto-login is **not** set up. After a restart or power cut, the Docker services (Vaultwarden, Seerr, Servarr) stay down until Aadil **logs in manually over RDP**. Vaultwarden is public, so its outage is visible from outside.
- **WSL2 updates can break Docker Desktop.**
- **This box can't be moved to Linux or Proxmox** (see the warning at the top). The plan is to replace it with a purpose-built server: better Jellyfin performance and proper hard drives in RAID. Until then, keep Vaultwarden's data backed up somewhere off this laptop. See [Roadmap](../roadmap.md).

## Monitoring

- ✅ A [Dozzle](../services/dozzle.md) agent (`dozzle-agent`) is running here and visible in the main Dozzle on the Monitoring Pi. ❓ Does it use port 7007 over the LAN or over Tailscale?
