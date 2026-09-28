# Dell OptiPlex 7050 — Proxmox host

The main server. Runs Proxmox VE; see [Proxmox host](../proxmox/host.md) for the hypervisor side and the [IP / CTID table](../proxmox/ip-ctid-table.md) for guests.

| | |
|---|---|
| **CPU** | Intel Core i7-6700 (4C/8T) |
| **RAM** | ❓ total — dashboard showed ~20.3 GiB used at 87%, so roughly 24 GB installed |
| **Boot / root disk** | ❓ model/size — ~50 GB free on root (last seen) |
| **Storage** | ZFS in use (ARC visible on memory graph) — ❓ pool name, layout, disks |
| **NICs** | ❓ |
| **IP / hostname** | ❓ |
| **Proxmox UI** | `https://<ip>:8006` ❓ |
| **Location** | ❓ |

## Load notes

- RAM sits around **87%**, but ~3 GiB of that is ZFS ARC (cache that shrinks under pressure). Swap use is minimal (~128 MiB), so nothing is actually starved.
- The biggest RAM consumers are almost certainly the **TrueNAS VM**, the **Immich VM**, and the Docker-in-LXC containers **CT 105** and **CT 108**.
- CPU idles at a few percent.

## Gotchas

- ❓ Anything about the BIOS (e.g. VT-d / virtualization settings for passthrough to TrueNAS)?
