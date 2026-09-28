# TrueNAS

> **What / why:** Network storage for the lab. Holds the ~900 GB Immich photo/media library.

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | `PVE-7050` ([Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md)) → **VM 104** |
| **Type + ID** | VM — VMID 104 |
| **Version** | ❓ TrueNAS SCALE / CORE, version |
| **LAN IP** | `10.10.0.22` — note: **doesn't** follow the CTID-suffix convention |
| **RAM allocated** | 10 GB |

## Disks

**SATA controller passthrough.** The OptiPlex's onboard SATA controller is passed through whole to this VM, and TrueNAS manages the physical disk directly — not via Proxmox storage.

| Pool | Layout | Disks | Usable size |
|---|---|---|---|
| ❓ | ❓ — single disk, so no mirror/raidz possible | 1× 4 TB WD SATA SSD | ❓ |

!!! danger "No redundancy"
    There's only **one** physical disk here. Whatever pool TrueNAS creates on it (almost certainly ZFS, TrueNAS's default) has **no redundancy** — a drive failure means total loss of the ~900 GB Immich library unless there's a working off-box backup. See Backups below (still ❓).

## Shares

| Share | Protocol | Used by |
|---|---|---|
| ❓ | NFS / SMB ❓ | [Immich](immich.md) |
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
