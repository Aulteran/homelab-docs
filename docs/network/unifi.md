# UniFi

The home network runs on UniFi hardware.

## Devices

| Device | Model | IP |
|---|---|---|
| Gateway | UniFi UX7 | ❓ |
| Switch | USW-Flex-Mini-5-port (connected to the UX7) | ❓ |
| AP(s) | ❓ | ❓ |

## Controller

- Where the UniFi Network application runs: ❓ (on the gateway / a Cloud Key / self-hosted?)

## Config worth recording

- DHCP range and static reservations: ❓ (copy reservations into the [IP / CTID table](../proxmox/ip-ctid-table.md))
- DNS handed out by DHCP: should point to [AdGuard Home](dns-adguard.md) ❓
- Port forwards: see [Network → Port forwards](index.md#port-forwards)
- VLANs / firewall rules: ❓
