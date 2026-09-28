# Proxmox host

Proxmox VE runs on the [Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md).

| | |
|---|---|
| **Proxmox version** | ❓ |
| **Node name / hostname** | `PVE-7050` |
| **LAN IP** | `10.10.0.15` |
| **Domain URL** | [pve-7050.andrims.net](https://pve-7050.andrims.net) |
| **Web UI (LAN)** | [https://10.10.0.15:8006](https://10.10.0.15:8006) |
| **Storage** | ZFS ❓ (pool names, what's on each) |
| **Backups** | ❓ vzdump schedule? Proxmox Backup Server? Where do backups land? |
| **Updates** | ❓ no-subscription repo? how often? |

## How things get deployed here

**Default: an LXC from the [Proxmox VE community helper scripts](https://community-scripts.github.io/ProxmoxVE/).** It keeps things simple. Use a VM only when the workload needs one (e.g. TrueNAS, Immich).

When running a helper script:

- Pick **Advanced** and size it down. LXC resource limits are ceilings, not reservations, but small limits keep things honest.
- Give it a **static IP** and add it to the [IP / CTID table](ip-ctid-table.md) right away.
- Add a [changelog](../changelog.md) entry and a service page from the [template](../templates/service.md).

## Docker on Proxmox

Docker containers are spread across multiple LXCs and VMs on the Windows side ([Server-GF65](../hardware/msi-gf65.md)). On Proxmox itself, most services run as native LXCs from community helper scripts rather than Docker-in-LXC — **CT 108 is the one guest whose contents are still unidentified**; it may be a Docker host. See the [IP / CTID table](ip-ctid-table.md).

## Resource picture

RAM runs around 87% used, including ~3 GiB of ZFS ARC; swap is barely touched. The heavy consumers are the TrueNAS VM (104) and the Immich VM (107); the LXCs (NPM, AdGuard, Forgejo, Paperless, cloudflared, ddns-updater, ActualBudget, and unidentified CT 108) are individually much lighter. See [hardware page](../hardware/optiplex-7050.md#load-notes).
