# Immich

> **What / why:** Self-hosted photo and video library (Google Photos replacement).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | `PVE-7050` ([Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md)) → **VM ❓** |
| **Type + ID** | VM — ❓ VMID / name |
| **LAN IP / port** | ❓ (default 2283) |

## How it was deployed

❓ Helper script, or Docker compose inside the VM?

## Access

| | |
|---|---|
| **Domain URL** | [immich.andrims.net](https://immich.andrims.net) |
| **LAN URL** | ❓ `http://<LAN IP>:2283` |
| **Public?** | **Yes** |
| **Path today** | Cloudflare Tunnel (`cloudflared`) → Immich. **Does not go through NPM.** |
| **cloudflared runs on** | ❓ (inside the Immich VM? a separate LXC?) |
| **NPM proxy host** | None yet — see planned move below |
| **Login** | (Vaultwarden → "Immich") ❓ |
| **DB password** | (Vaultwarden → "Immich DB") |

## Planned: move from Cloudflare Tunnel to direct IP + NPM

🟡 Planned, not started. Make Immich work like Jellyfin: a **DNS-only** Cloudflare record for `immich.andrims.net` pointing at the home IP, with Nginx Proxy Manager terminating TLS and forwarding to Immich.

Why:

- **Upload size limit.** Cloudflare's proxy (tunnels included) caps request bodies at **100 MB on the free plan**, so large video uploads from the phone app can fail through the tunnel.
- **Consistency.** One public path (port forward → NPM) for everything, instead of two different setups to troubleshoot.
- Cloudflare's terms discourage serving large amounts of video through its proxy.

Checklist:

- [ ] NPM proxy host `immich.andrims.net` → Immich LAN IP:2283, websockets on, Let's Encrypt cert. Set client max body size high (e.g. `client_max_body_size 50000M;` in the advanced tab).
- [ ] In Cloudflare, remove the tunnel's public hostname for `immich.andrims.net` and replace it with a DNS-only A record to the home IP.
- [ ] Test from outside the LAN (phone on mobile data), including a large video upload.
- [ ] Stop and remove `cloudflared` if nothing else uses the tunnel.
- [ ] Update this page, [Cloudflare](../network/cloudflare.md), [Reverse proxy](../network/reverse-proxy.md), [Services](index.md), and the [changelog](../changelog.md).

## Data

- Library: ~**900 GB** of photos/media on [TrueNAS](truenas.md).
- ❓ How the VM mounts it (NFS / SMB), share name, mount path.
- ❓ Upload location vs. external library?
- Postgres database: ❓ where it lives (local VM disk?).

## Backups

- ❓ Is the Immich Postgres DB dumped anywhere? (The library alone isn't enough to restore albums, faces, users.)
- ❓ Are the 900 GB of originals backed up anywhere besides TrueNAS?

## Updates

❓

## Gotchas

- One of the biggest RAM users on the Proxmox host (machine learning container).
- **Publicly exposed.** Keep it updated and use strong passwords on every user.
