# Dell OptiPlex 7050 SFF — Proxmox host

The main server. Runs Proxmox VE; see [Proxmox host](../proxmox/host.md) for the hypervisor side and the [IP / CTID table](../proxmox/ip-ctid-table.md) for guests.

| | |
|---|---|
| **CPU** | Intel Core i7-6700 (4C/8T) |
| **RAM** | ❓ total — dashboard showed ~20.3 GiB used at 87%, so roughly 24 GB installed |
| **Boot / root disk** | ❓ model/size — ~50 GB free on root (last seen) |
| **Storage** | ZFS in use (ARC visible on memory graph) — ❓ pool name, layout, disks |
| **NICs** | ❓ |
| **Form factor** | SFF (small form factor) |
| **LAN IP** | `10.10.0.15` |
| **Hostname** | `PVE-7050` |
| **Proxmox domain** | [pve-7050.andrims.net](https://pve-7050.andrims.net) |
| **Proxmox UI (LAN)** | [https://10.10.0.15:8006](https://10.10.0.15:8006) |
| **Location** | ❓ |

## Load notes

- RAM sits around **87%**, but ~3 GiB of that is ZFS ARC (cache that shrinks under pressure). Swap use is minimal (~128 MiB), so nothing is actually starved.
- The biggest RAM consumers are almost certainly the **TrueNAS VM (104)** and the **Immich VM (107)**. The LXCs (NPM, AdGuard, Forgejo, Paperless-ngx, cloudflared, ddns-updater, ActualBudget) are typically much lighter — except possibly **CT 108**, still unidentified and worth checking if it turns out to be a Docker host.
- CPU idles at a few percent.

## Gotchas

- ❓ Anything about the BIOS (e.g. VT-d / virtualization settings for passthrough to TrueNAS)?
