# Jellyfin

> **What / why:** Media server for movies and TV, fed by the [Servarr stack](servarr.md).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | `Server-GF65` ([MSI GF65](../hardware/msi-gf65.md), Windows 10 Pro) |
| **Type** | Native Windows install (bare metal, not Docker), on the boot SSD. Runs as the Jellyfin **tray app**; it does **not** appear in Windows Services. |
| **LAN IP / port** | `10.10.0.140` : 8096 (default — confirm) |
| **Transcoding** | On the GF65 itself, with **NVENC hardware acceleration** on the GTX 1660 Ti |

## Access

| | |
|---|---|
| **Domain URL** | [stream.andrims.net](https://stream.andrims.net) |
| **LAN URL** | [http://10.10.0.140:8096](http://10.10.0.140:8096) |
| **Public?** | **Yes** |
| **Path** | Cloudflare DNS-only record → home IP → Nginx Proxy Manager → Jellyfin |
| **Admin login** | (Vaultwarden → "Jellyfin") ❓ |

See [Cloudflare](../network/cloudflare.md) and [Reverse proxy](../network/reverse-proxy.md). Planned change to stop publishing the home IP: [Edge VPS](../network/edge-vps.md); other options and costs: [Jellyfin exposure options](../network/jellyfin-exposure-options.md).

## Data

- Media library: on **Neo**, the 2 TB Seagate USB hard disk plugged into the GF65. ❓ drive letter / path
- Config / metadata: on the 256 GB boot SSD (nearly always full). ❓ path

## Backups

- ❓ Config/metadata backed up? (Media itself can be re-acquired; watch history and users can't.)

## Updates

Currently on **Jellyfin 12**. ❓ How updates are applied (manual installer?).

## Gotchas

- **Publicly exposed** (along with [Immich](immich.md)). Keep it updated and use strong passwords on every user.
- Offloading transcoding to the M1 MacBook Air was considered and dropped — the MacBook is too weak to be worth it.
- Goes down with the GF65. Jellyfin isn't in Docker, and it **starts on boot** on its own, so the Docker Desktop login issue on the [hardware page](../hardware/msi-gf65.md#known-risks) doesn't apply to it.
- **Known issue: the server randomly stops since Jellyfin 12.** Since updating to Jellyfin 12, the Jellyfin server turns itself off at random times. It isn't a Windows service, so Windows service recovery options can't restart it. **Fix:** RDP into Server-GF65 and start the server again from the Jellyfin tray app. No cause found yet. Until it is, [Uptime Kuma](uptime-kuma.md) should watch `stream.andrims.net` and the LAN URL so an outage gets noticed, since this one is public. ❓ Check the Jellyfin logs and Windows Event Viewer around the times it stops. ❓ How does it start on boot without a login (startup task?).
- **Neo is the only copy of the media on a single USB drive**, and Jellyfin's metadata sits on a nearly full boot disk.
