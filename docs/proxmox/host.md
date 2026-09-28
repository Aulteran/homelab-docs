# Proxmox host

Proxmox VE runs on the [Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md).

| | |
|---|---|
| **Proxmox version** | ❓ |
| **Node name / hostname** | `PVE-7050` |
| **LAN IP** | `10.10.0.15` |
| **Domain URL** | [pve-7050.andrims.net](https://pve-7050.andrims.net) |
| **Web UI (LAN)** | [https://10.10.0.15:8006](https://10.10.0.15:8006) |
| **Storage** | No ZFS — local storage is LVM (`local`) + LVM-thin (`local-lvm`) on the 512 GB boot SSD. See [hardware page](../hardware/optiplex-7050.md) for the 4 TB drive passed through to TrueNAS. |
| **Backups** | ❓ vzdump schedule? Proxmox Backup Server? Where do backups land? |
| **Updates** | ❓ no-subscription repo? how often? |

## How things get deployed here

**Default: an LXC from the [Proxmox VE community helper scripts](https://community-scripts.github.io/ProxmoxVE/).** It keeps things simple. Use a VM only when the workload needs one (e.g. TrueNAS, Immich).

When running a helper script:

- Pick **Advanced** and size it down. LXC resource limits are ceilings, not reservations, but small limits keep things honest.
- Give it a **static IP** and add it to the [IP / CTID table](ip-ctid-table.md) right away.
- Add a [changelog](../changelog.md) entry and a service page from the [template](../templates/service.md).

## Docker on Proxmox

Docker containers are spread across multiple LXCs and VMs on the Windows side ([Server-GF65](../hardware/msi-gf65.md)). On Proxmox itself, most services run as native LXCs from community helper scripts rather than Docker-in-LXC. **CT 100, 103, 105 and 108 are being consolidated**: 103 and 108 are old Docker-in-LXC hosts already retired, and 100 and 105 (Paperless-ngx) are planned to move onto one new **"docker" CT** for lightweight Compose services — after which all four old CTs get deleted. See the [IP / CTID table](ip-ctid-table.md) and [Roadmap](../roadmap.md#planned).

## Resource picture

RAM runs around 87% used (~20.3 of 24 GiB); swap is barely touched. The host has no ZFS, so most of that is VM allocation: TrueNAS (VM 104, 10 GB) and Immich (VM 107, 7 GB) account for 17 GB between them; the rest covers Proxmox itself and the LXCs (NPM, AdGuard, Forgejo, Paperless, cloudflared, ddns-updater, ActualBudget), which are individually much lighter. CT 100, 103 and 108 are old/retiring. See [hardware page](../hardware/optiplex-7050.md#load-notes).
