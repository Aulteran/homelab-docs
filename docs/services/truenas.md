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

## Disks

❓ How are the disks given to the VM — HBA/controller passthrough, individual disk passthrough, or virtual disks on Proxmox ZFS?

| Pool | Layout | Disks | Usable size |
|---|---|---|---|
| ❓ | ❓ | ❓ | ❓ |

## Shares

| Share | Protocol | Used by |
|---|---|---|
| ❓ | NFS / SMB ❓ | [Immich](immich.md) |
| ❓ | | Jellyfin media? Paperless? |

## Access

| | |
|---|---|
| **Domain URL** | [truenas.andrims.net](https://truenas.andrims.net) |
| **LAN URL** | ❓ `http://10.10.0.22` |
| **Login** | (Vaultwarden → "TrueNAS") ❓ |

## Backups

- ❓ Snapshot schedule?
- ❓ Any off-box copy (replication, cloud sync, USB)?

## Gotchas

- One of the biggest RAM users on the Proxmox host (ZFS inside TrueNAS wants RAM too).
