# TrueNAS

> **What / why:** Network storage for the lab. Holds the ~900 GB Immich photo/media library.

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | Proxmox — [Dell OptiPlex 7050](../hardware/optiplex-7050.md) |
| **Type + ID** | VM — ❓ VMID |
| **Version** | ❓ TrueNAS SCALE / CORE, version |
| **IP** | ❓ |

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
| **Web UI** | ❓ |
| **Login** | (Vaultwarden → "TrueNAS") ❓ |

## Backups

- ❓ Snapshot schedule?
- ❓ Any off-box copy (replication, cloud sync, USB)?

## Gotchas

- One of the biggest RAM users on the Proxmox host (ZFS inside TrueNAS wants RAM too).
