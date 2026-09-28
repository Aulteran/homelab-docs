# Jellyfin

> **What / why:** Media server for movies and TV, fed by the [Servarr stack](servarr.md).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | `Server-GF65` ([MSI GF65](../hardware/msi-gf65.md), Windows 10 Pro) |
| **Type** | ❓ Docker Desktop container or native Windows install? |
| **LAN IP / port** | `10.10.0.140` : 8096 (default — confirm) |
| **Transcoding** | On the GF65 itself — ❓ hardware acceleration (which GPU / method) or software |

## Access

| | |
|---|---|
| **Domain URL** | [stream.andrims.net](https://stream.andrims.net) |
| **LAN URL** | [http://10.10.0.140:8096](http://10.10.0.140:8096) |
| **Public?** | **Yes** |
| **Path** | Cloudflare DNS-only record → home IP → Nginx Proxy Manager → Jellyfin |
| **Admin login** | (Vaultwarden → "Jellyfin") ❓ |

See [Cloudflare](../network/cloudflare.md) and [Reverse proxy](../network/reverse-proxy.md).

## Data

- Media library: ❓ path / drive
- Config / metadata: ❓ path

## Backups

- ❓ Config/metadata backed up? (Media itself can be re-acquired; watch history and users can't.)

## Updates

❓

## Gotchas

- **Publicly exposed** (along with [Immich](immich.md)). Keep it updated and use strong passwords on every user.
- Offloading transcoding to the M1 MacBook Air was considered and dropped — the MacBook is too weak to be worth it.
- Goes down with the GF65 (see Docker Desktop login issue on the [hardware page](../hardware/msi-gf65.md#known-risks)).
