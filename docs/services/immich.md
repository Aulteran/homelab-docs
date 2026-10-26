# Immich

> **What / why:** Self-hosted photo and video library (Google Photos replacement).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | `PVE-7050` ([Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md)) → **VM 107** (`debian-immich`) — 6 vCPUs, 7 GB RAM, 20 GB boot disk + 32 GB database disk |
| **Type + ID** | VM — VMID 107, Debian 12 (bookworm), cloud-init |
| **LAN IP / port** | `10.10.0.107` : 2283 |
| **Tailscale** | On the tailnet as `vm-107` (`100.110.0.107`) |
| **RAM allocated** | 7 GB, but `balloon: 6144` means the guest only sees ~6 GB. The iGPU passthrough pins all 7 GB on the host anyway. |
| **GPU** | Intel HD Graphics 530 passed through (`/dev/dri/renderD128` exists in the VM). The compose file doesn't map `/dev/dri` into any container, so ❓ Immich doesn't seem to use it yet. |

## How it was deployed

Docker Compose inside the Debian VM: `/root/docker/immich-docker-ct/docker-compose.yml`. Containers (2026-10-25):

| Container | Image |
|---|---|
| `immich_server` | `ghcr.io/immich-app/immich-server:release` |
| `immich_machine_learning` | `ghcr.io/immich-app/immich-machine-learning:release` (CPU, no hardware acceleration tag) |
| `immich_ml_balancer` | `apetersson/immich_ml_balancer:latest` — sits between the server and ML containers; ❓ which other ML workers it balances to |
| `immich_postgres` | `ghcr.io/immich-app/postgres:14-vectorchord0.4.3-pgvectors0.2.0` |
| `immich_redis` | `valkey/valkey:8-bookworm` |
| `dozzle-agent` | `amir20/dozzle:latest` (separate compose: `/root/docker/dozzle-agent/`) |

Immich version on 2026-10-25: **v3.3.5**.

## Access

| | |
|---|---|
| **Domain URL** | [immich.andrims.net](https://immich.andrims.net) |
| **LAN URL** | [http://10.10.0.107:2283](http://10.10.0.107:2283) |
| **Public?** | **Yes** |
| **Path today** | Cloudflare Tunnel (`cloudflared`) → Immich. **Does not go through NPM.** |
| **cloudflared runs on** | `PVE-7050` → **CT 102** (`cloudflared`), a separate LXC from the [community scripts](https://community-scripts.github.io/ProxmoxVE/) — `10.10.0.102` |
| **NPM proxy host** | None yet — see planned move below |
| **Login** | (Vaultwarden → "Immich") ❓ |
| **DB password** | (Vaultwarden → "Immich DB") |

## Planned: move from Cloudflare Tunnel to direct IP + NPM

!!! warning "Conflicts with the home-IP rule"
    Pointing `immich.andrims.net` at the home IP would break [Roadmap rule 4](../roadmap.md#standing-rules) (only `stream.andrims.net` uses the home IP). Do this move **through the planned [edge VPS](../network/edge-vps.md)** instead: the VPS forwards to Immich over Tailscale, so there's no upload cap and no home IP.

🟡 Planned, not started. Make Immich work like Jellyfin: a **DNS-only** Cloudflare record for `immich.andrims.net` pointing at the home IP, with Nginx Proxy Manager terminating TLS and forwarding to Immich.

Why:

- **Upload size limit.** Cloudflare's proxy (tunnels included) caps request bodies at **100 MB on the free plan**, so large video uploads from the phone app can fail through the tunnel.
- **Consistency.** One public path (port forward → NPM) for everything, instead of two different setups to troubleshoot.
- Cloudflare's terms discourage serving large amounts of video through its proxy.

Checklist:

- [ ] NPM proxy host `immich.andrims.net` → `10.10.0.107:2283`, websockets on, Let's Encrypt cert. Set client max body size high (e.g. `client_max_body_size 50000M;` in the advanced tab).
- [ ] In Cloudflare, remove the tunnel's public hostname for `immich.andrims.net` and replace it with a DNS-only A record to the home IP.
- [ ] Test from outside the LAN (phone on mobile data), including a large video upload.
- [ ] Leave the `cloudflared` LXC (CT 102) running — Vaultwarden and Seerr still use the tunnel.
- [ ] Update this page, [Cloudflare](../network/cloudflare.md), [Reverse proxy](../network/reverse-proxy.md), [Services](index.md), and the [changelog](../changelog.md).

## Data

- Library: on [TrueNAS](truenas.md), mounted over **NFS 4.1** from `10.10.0.22:/mnt/photo-storage/immich-data` at `/mnt/immich-data` (fstab: `hard`, `noatime`, `x-systemd.automount`). The share showed **1.6 TB used of 3.5 TB** on 2026-10-25 (it was ~900 GB when first documented).
- ❓ Upload location vs. external library?
- Postgres database: on its own 32 GB virtual disk (`scsi1`), mounted at `/mnt/immich-db-scsi-vdisk` (2.6 GB used). That disk is marked `backup=0` in Proxmox, so a vzdump of VM 107 would **skip** it.

## Backups

- **DB dumps:** Immich's built-in database backup runs daily at 02:00 and writes `immich-db-backup-*.sql.gz` (~770 MB each) to `/mnt/immich-data/immich/backups`. That folder is on the **same TrueNAS disk as the photos**, so one disk failure loses both.
- ❓ Are the originals backed up anywhere besides TrueNAS? (No off-box copy known.)

## Updates

Images follow the `release` tag (`IMMICH_VERSION` unset), so they update when the stack is pulled and recreated. The Debian VM has `unattended-upgrades` enabled; 7 packages were pending on 2026-10-25.

## Gotchas

- One of the biggest RAM users on the Proxmox host (machine learning container).
- **Publicly exposed.** Keep it updated and use strong passwords on every user.
- SSH into the VM allows **root login with a password** (`PermitRootLogin yes`, `PasswordAuthentication yes`).
- Something on port **9090** is socket-activated by systemd (likely Cockpit) — ❓ confirm and remove if unused.
