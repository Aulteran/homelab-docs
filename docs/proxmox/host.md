# Proxmox host

Proxmox VE runs on the [Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md).

| | |
|---|---|
| **Proxmox version** | Proxmox VE 9.2.20 |
| **Node name / hostname** | `PVE-7050` |
| **LAN IP** | `10.10.0.15` |
| **Domain URL** | [pve-7050.andrims.net](https://pve-7050.andrims.net) |
| **Web UI (LAN)** | [https://10.10.0.15:8006](https://10.10.0.15:8006) |
| **Kernel** | `7.0.14-19-pve` |
| **Storage** | No ZFS — local storage is LVM (`local`) + LVM-thin (`local-lvm`) on the 512 GB boot NVMe. See [hardware page](../hardware/optiplex-7050.md) for the 4 TB drive passed through to TrueNAS. |
| **Network** | `vmbr0` on the onboard NIC (`enp0s31f6`), static `10.10.0.15/24`, gateway `10.10.0.1`. The bridge is already **VLAN-aware** (`bridge-vids 2-4094`), which helps the planned [homelab VLAN](../roadmap.md#planned). |
| **Backups** | **None.** No vzdump job (`/etc/pve/jobs.cfg` is empty), no Proxmox Backup Server. Neither the host nor any VM or CT is backed up. Leftover dumps from Nov 2025 (VM 170, which no longer exists, VM 171 and CT 153) still take up 9.9 GB in `/var/lib/vz/dump`. |
| **Updates** | Manual, no schedule — updated by hand whenever it gets remembered. Uses the **`pve-no-subscription`** repo (enterprise and Ceph enterprise repos are disabled). 38 packages pending on 2026-10-25. |
| **Firewall** | Proxmox firewall **disabled** at the datacenter level |
| **Notifications** | None configured (`/etc/pve/notifications.cfg` is empty), so no alerts for SMART or backup failures |
| **SSH** | `PermitRootLogin yes` and `PasswordAuthentication yes` — root can log in with a password |
| **Extra on the host** | [Tailscale](../network/tailscale.md) (app connector) and `glance-agent` (`/opt/glance-agent/agent`, feeds the [Glance](../services/glance.md) dashboard) |

!!! warning "No backups"
    Nothing on this host is backed up. If the 512 GB boot SSD dies, every guest disk on it (NPM, AdGuard, Forgejo, ActualBudget, Paperless, the Immich VM and the TrueNAS boot disk) goes with it. A vzdump schedule to a separate disk or machine is the first fix. See the [roadmap](../roadmap.md#known-risks-tech-debt).

## How things get deployed here

**Default: an LXC from the [Proxmox VE community helper scripts](https://community-scripts.github.io/ProxmoxVE/).** It keeps things simple. Use a VM only when the workload needs one (e.g. TrueNAS, Immich).

When running a helper script:

- Pick **Advanced** and size it down. LXC resource limits are ceilings, not reservations, but small limits keep things honest.
- Give it a **static IP** and add it to the [IP / CTID table](ip-ctid-table.md) right away.
- Add a [changelog](../changelog.md) entry and a service page from the [template](../templates/service.md).

## Docker on Proxmox

Outside Proxmox, Docker runs on the Windows side ([Server-GF65](../hardware/msi-gf65.md), Docker Desktop). On Proxmox itself, most services run as native LXCs from community helper scripts. Lightweight Docker Compose stacks go on **one** shared Docker host, **CT 105 (`docker`)**, instead of each getting its own CT — currently the [docs site](../services/docs-site.md) nginx container, and [Paperless-ngx](../services/paperless-ngx.md). The old one-off and Docker-in-LXC containers (CT 100, 103, 108) have been deleted. See the [IP / CTID table](ip-ctid-table.md).

## Resource picture

RAM runs around 87% used (~20.3 of 24 GiB); swap is barely touched. The host has no ZFS, so most of that is VM allocation.

| | Allocated RAM |
|---|---|
| Running VMs — TrueNAS (VM 104, 10 GB) + Immich (VM 107, 7 GB) | 17 GB |
| All LXCs (101, 102, 105, 106, 109, 110, 153) | ~7.6 GB |
| **Total allocated, running guests** | **~24.6 GB** — more than the 24 GB installed |
| Home Assistant OS (VM 171) — currently **stopped** | +2 GB if started |

The overcommit is fine for now because LXC memory limits are ceilings, not reservations, and the containers use far less than their limits (on 2026-10-25 all seven LXCs together used about 1.4 GB). VM RAM is different: both running VMs have PCI passthrough, so Proxmox pins their **entire** allocation and ballooning does nothing on the host side. VM 107 has `balloon: 6144`, so Immich only sees ~6 GB while the host still holds 7 GB for it. **Starting HAOS (VM 171) would push VM allocation to 19 GB**, so trim something (e.g. Immich's 7 GB) or add RAM before making it a permanent guest. See [hardware page](../hardware/optiplex-7050.md#load-notes).
