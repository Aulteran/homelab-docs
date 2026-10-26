# TrueNAS

> **What / why:** Network storage for the lab. Holds the Immich photo/media library (1.6 TB on 2026-10-25).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | `PVE-7050` ([Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md)) → **VM 104** (`truenas`) — 10 GB RAM, 16 GB boot disk |
| **Type + ID** | VM — VMID 104 |
| **Version** | TrueNAS **SCALE** (it's Linux-based: `ostype: l26`, and it joins the tailnet as `truenas-scale`) — ❓ exact version |
| **LAN IP** | `10.10.0.22` — note: **doesn't** follow the CTID-suffix convention |
| **Tailscale** | On the tailnet as `truenas-scale` (`100.110.0.22`) |
| **vCPUs / RAM** | 2 vCPUs (`cpu: host`) / 10 GB, all pinned on the host because of the SATA passthrough |
| **Starts** | First on boot (`startup: order=1,up=120`), 120 s before Immich |
| **SSH** | ❓ No SSH access set up from the workstation yet |

## Disks

**SATA controller passthrough.** The OptiPlex's onboard SATA controller is passed through whole to this VM, and TrueNAS manages the physical disk directly — not via Proxmox storage.

| Pool | Layout | Disks | Usable size |
|---|---|---|---|
| `photo-storage` (from the share path) | ❓ — single disk, so no mirror/raidz possible | 1× 4 TB WD SATA SSD | ~3.5 TB (as seen by the Immich NFS client; 1.6 TB used on 2026-10-25) |

!!! danger "No redundancy"
    There's only **one** physical disk here. Whatever pool TrueNAS creates on it (almost certainly ZFS, TrueNAS's default) has **no redundancy** — a drive failure means total loss of the 1.6 TB Immich library **and** Immich's database dumps, which are stored on the same share. See Backups below (still ❓).

## Shares

| Share | Protocol | Used by |
|---|---|---|
| `/mnt/photo-storage/immich-data` | NFS 4.1 | [Immich](immich.md) (VM 107, mounted at `/mnt/immich-data`) |
| ❓ | | Jellyfin media? Paperless? |

## Access

| | |
|---|---|
| **Domain URL** | [truenas.andrims.net](https://truenas.andrims.net) |
| **LAN URL** | [http://10.10.0.22](http://10.10.0.22) (login at `/ui/signin`) |
| **Login** | (Vaultwarden → "TrueNAS") ❓ |

## Backups

- ❓ Snapshot schedule?
- ❓ Any off-box copy (replication, cloud sync, USB)?

## Gotchas

- One of the biggest RAM users on the Proxmox host — **10 GB** allocated (ZFS inside TrueNAS wants RAM too).
- **Single-disk pool, no redundancy** — see the warning above. This is the most important gap to close in [Backups](#backups).
- One NFS client and one disk probably don't need 10 GB; TrueNAS SCALE's minimum is 8 GB. Trimming it would free RAM for HAOS.
