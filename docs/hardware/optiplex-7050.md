# Dell OptiPlex 7050 SFF — Proxmox host

The main server. Runs Proxmox VE; see [Proxmox host](../proxmox/host.md) for the hypervisor side and the [IP / CTID table](../proxmox/ip-ctid-table.md) for guests.

| | |
|---|---|
| **CPU** | Intel Core i7-6700 (4C/8T) |
| **RAM** | 24 GB (8 GB × 3 sticks) — matches the ~20.3 GiB / 87% seen on the dashboard |
| **Boot / root disk** | 512 GB **NVMe** SSD (Kingston OM8PCP3512F) — split into LVM (`local`, 96 GB root for ISOs/backups) + LVM-thin (`local-lvm`, 348 GB for VM/CT disks), Proxmox's default layout. 46 GB free on root, thin pool 21% used (2026-10-25). **SMART: passed, 46% wear used, 68 TB written** (2026-10-25). |
| **Storage** | **No ZFS at the Proxmox host level.** Local storage is the LVM/LVM-thin split above. A separate **4 TB WD SATA SSD** is plugged into the motherboard's SATA ports, and the whole onboard SATA controller (`0000:00:17`) is passed through to [TrueNAS](../services/truenas.md) (VM 104) — TrueNAS manages that disk (and any ZFS on it) itself, not Proxmox. |
| **iGPU** | Intel HD Graphics 530 (`0000:00:02`) — passed through to [Immich](../services/immich.md) (VM 107) |
| **NICs** | 1× onboard Intel I219-LM 1 GbE (`enp0s31f6`), the `vmbr0` bridge port. `/etc/network/interfaces` still has stanzas for `enp4s0` ("BOTTOM PORT") and `enp5s0` ("WAN TOP PORT") from a card that's no longer installed (`lspci` shows no such devices), plus a leftover `r8168-dkms` driver config. Harmless, but can be cleaned up. |
| **Expansion slots** | SLOT1 PCIe 2.0 x4 (low profile) and SLOT2 PCIe 3.0 x16 (low profile) — **both empty** (2026-10-26) |
| **Form factor** | SFF (small form factor) |
| **LAN IP** | `10.10.0.15` |
| **Hostname** | `PVE-7050` |
| **Proxmox domain** | [pve-7050.andrims.net](https://pve-7050.andrims.net) |
| **Proxmox UI (LAN)** | [https://10.10.0.15:8006](https://10.10.0.15:8006) |
| **Tailscale IP** | `100.110.0.15` |

## Load notes

- RAM sits around **87%** (~20.3 GiB of 24 GB). No ZFS on the host, so that isn't ARC — it's mostly VM allocations: [TrueNAS](../services/truenas.md) (VM 104) is given **10 GB** and [Immich](../services/immich.md) (VM 107) **7 GB**, which is 17 GB on its own. Both VMs have PCI passthrough, so Proxmox **pins all of their RAM**; it can't be ballooned or shared. The rest covers Proxmox itself and the LXCs (NPM, cloudflared, docker, Forgejo, ddns-updater, ActualBudget, AdGuard), which are individually light. Swap use is low (~420 MiB on 2026-10-25), so nothing is actually starved.
- On paper, running guests are allocated ~24.6 GB — slightly more than the 24 GB installed. That works because LXC limits are ceilings. The stopped **Home Assistant OS VM (VM 171, 2 GB)** would add to the VM share if started — see [Proxmox host → Resource picture](../proxmox/host.md#resource-picture).
- CT 100, 103 and 108 have been deleted.
- CPU idles at a few percent. The CPU governor is `performance` (`intel_pstate`), not `powersave`.
- No `lm-sensors` installed, so CPU temperatures aren't read on the host.

## Gotchas

- VT-d / IOMMU must be enabled in the BIOS for the SATA controller passthrough to TrueNAS to work — since that passthrough is confirmed working, this is set correctly. The kernel command line has `intel_iommu=on iommu=pt`.
- At boot the kernel logs a few `DMAR: [DMA Write NO_PASID] Request device [00:02.0]` faults — that's the iGPU being handed to VM 107.
- **The host has no SATA driver.** `ahci` is blacklisted in `/etc/modprobe.d/pve-blacklist.conf` and the SATA controller (`8086:a282`) is claimed by `vfio-pci` in `/etc/modprobe.d/vfio.conf`, so it can go to TrueNAS. Any SATA disk plugged in is invisible to Proxmox itself.
- The iGPU is **not** pinned to `vfio-pci` in config; it's only taken by VM 107's `hostpci0` when the VM starts. Removing that line and rebooting gives it back to the host's `i915` driver.
- **Boot NVMe wear:** 46% used after 68 TB written. It's healthy, but it holds every guest disk and nothing is backed up, so check `smartctl -A /dev/nvme0n1` now and then.
