# Tailscale

Tailscale mesh networking is the remote-access path into the lab. Internal-only services (Forgejo, docs site, monitoring) are reached over Tailscale rather than exposed publicly.

| | |
|---|---|
| **Tailnet name** | ❓ |
| **Admin login** | ❓ (which identity provider) |
| **MagicDNS** | ❓ |
| **DNS** | ❓ AdGuard set as the tailnet nameserver? |
| **Subnet router / exit node** | ❓ which device, which routes |

## Devices on the tailnet

| Device | Tailscale IP | Notes |
|---|---|---|
| ❓ | 100.x.x.x | |

## Planned use

- [Dozzle](../services/dozzle.md): point the main instance at agents' **Tailscale IPs** instead of opening port 7007 on the LAN (especially the Windows GF65).
