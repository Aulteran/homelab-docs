# Dell OptiPlex 7050 SFF — Proxmox host

The main server. Runs Proxmox VE; see [Proxmox host](../proxmox/host.md) for the hypervisor side and the [IP / CTID table](../proxmox/ip-ctid-table.md) for guests.

| | |
|---|---|
| **CPU** | Intel Core i7-6700 (4C/8T) |
| **RAM** | 24 GB (8 GB × 3 sticks) — matches the ~20.3 GiB / 87% seen on the dashboard |
| **Boot / root disk** | 512 GB SSD — split into LVM (`local`, ISOs/backups) + LVM-thin (`local-lvm`, VM/CT disks), Proxmox's default layout. ~50 GB free on root (last seen). |
| **Storage** | **No ZFS at the Proxmox host level.** Local storage is the LVM/LVM-thin split above. A separate **4 TB WD SATA SSD** is plugged into the motherboard's SATA ports and passed through whole to [TrueNAS](../services/truenas.md) (VM 104) — TrueNAS manages that disk (and any ZFS on it) itself, not Proxmox. |
| **NICs** | 1× onboard 1 GbE — no additional NICs installed |
| **Form factor** | SFF (small form factor) |
| **LAN IP** | `10.10.0.15` |
| **Hostname** | `PVE-7050` |
| **Proxmox domain** | [pve-7050.andrims.net](https://pve-7050.andrims.net) |
| **Proxmox UI (LAN)** | [https://10.10.0.15:8006](https://10.10.0.15:8006) |
| **Tailscale IP** | `100.110.0.15` |

## Load notes

- RAM sits around **87%** (~20.3 GiB of 24 GB). No ZFS on the host, so that isn't ARC — it's mostly VM allocations: [TrueNAS](../services/truenas.md) (VM 104) is given **10 GB** and [Immich](../services/immich.md) (VM 107) **7 GB**, which is 17 GB on its own. The rest covers Proxmox itself and the LXCs (NPM, cloudflared, docker, Forgejo, ddns-updater, ActualBudget, AdGuard), which are individually light. Swap use is minimal (~128 MiB), so nothing is actually starved.
- On paper, running guests are allocated ~24.6 GB — slightly more than the 24 GB installed. That works because LXC limits are ceilings. The stopped **Home Assistant OS VM (VM 171, 2 GB)** would add to the VM share if started — see [Proxmox host → Resource picture](../proxmox/host.md#resource-picture).
- CT 100, 103 and 108 have been deleted.
- CPU idles at a few percent.

## Gotchas

- VT-d / IOMMU must be enabled in the BIOS for the SATA controller passthrough to TrueNAS to work — since that passthrough is confirmed working, this is set correctly.
