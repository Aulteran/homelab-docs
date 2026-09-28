# Jellyfin

> **What / why:** Media server for movies and TV, fed by the [Servarr stack](servarr.md).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | [MSI GF65](../hardware/msi-gf65.md) (Windows 10 Pro) |
| **Type** | ❓ Docker Desktop container or native Windows install? |
| **IP / ports** | ❓ (default 8096) |
| **Transcoding** | Hardware transcoding via the [M1 MacBook Air](../hardware/macbook-air.md) — ❓ how it's wired up |

## Access

| | |
|---|---|
| **URL** | `https://stream.andrims.net` |
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

- It's the **only publicly exposed** service. Keep it updated and use strong passwords on every user.
- Goes down with the GF65 (see Docker Desktop login issue on the [hardware page](../hardware/msi-gf65.md#known-risks)).
