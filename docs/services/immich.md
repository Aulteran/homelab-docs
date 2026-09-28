# Immich

> **What / why:** Self-hosted photo and video library (Google Photos replacement).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | Proxmox — [Dell OptiPlex 7050](../hardware/optiplex-7050.md) |
| **Type + ID** | VM — ❓ VMID |
| **IP / ports** | ❓ (default 2283) |

## How it was deployed

❓ Helper script, or Docker compose inside the VM?

## Access

| | |
|---|---|
| **URL** | ❓ |
| **NPM proxy host** | ❓ |
| **Internal / external** | ❓ |
| **Login** | (Vaultwarden → "Immich") ❓ |
| **DB password** | (Vaultwarden → "Immich DB") |

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
