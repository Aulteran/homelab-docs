# Hardware

| Machine | Hostname | LAN IP | OS | Role | Always on? |
|---|---|---|---|---|---|
| [Dell OptiPlex 7050 SFF](optiplex-7050.md) | `PVE-7050` | `10.10.0.15` | Proxmox VE | Main virtualization host | ✅ |
| [MSI GF65](msi-gf65.md) | `Server-GF65` | `10.10.0.140` | Windows 10 Pro + Docker Desktop | Media + Vaultwarden Docker host | ❓ |
| [Monitoring Pi](monitoring-pi.md) | ❓ | ❓ | ❓ (Raspberry Pi OS?) | Monitoring only | ✅ |
| [MSI Raider GE68 HX](msi-raider-ge68hx.md) | ❓ | DHCP | Windows 11 | Main personal workstation (client, not a server) | No |
| [M1 MacBook Air](macbook-air.md) | ❓ | DHCP | macOS | Secondary personal computer (client, no lab role) | No |

!!! tip "Keep LAN IPs in sync"
    Servers should have a **DHCP reservation in UniFi** (or a static IP) so these don't drift. When an IP changes, update this table, the [IP / CTID table](../proxmox/ip-ctid-table.md), and the LAN links on the [Services](../services/index.md) page in the same commit.
