# Services

New services start from the [service template](../templates/service.md).

| Service | Status | Host | URL | Critical? |
|---|---|---|---|---|
| [Vaultwarden](vaultwarden.md) | ✅ | MSI GF65 (Docker Desktop) | ❓ | **Yes** |
| [Immich](immich.md) | ✅ | Proxmox VM | ❓ | **Yes** |
| [TrueNAS](truenas.md) | ✅ | Proxmox VM | ❓ | **Yes** |
| [Paperless-ngx](paperless-ngx.md) | ✅ | ❓ | ❓ | **Yes** |
| [Jellyfin](jellyfin.md) | ✅ | MSI GF65 | `stream.andrims.net` | No |
| [Servarr stack](servarr.md) | ✅ | MSI GF65 (Docker Desktop) | ❓ | No |
| [Glance](glance.md) | ✅ | Monitoring Pi | ❓ | No |
| [Nginx Proxy Manager](../network/reverse-proxy.md) | ✅ | ❓ | ❓ | **Yes** |
| [AdGuard Home](../network/dns-adguard.md) | ✅ | ❓ | ❓ | **Yes** |
| [Dozzle](dozzle.md) | 🟡 | Monitoring Pi + agents | — | No |
| [Uptime Kuma](uptime-kuma.md) | 🟡 | Monitoring Pi | — | No |
| [Forgejo](forgejo.md) | 🟡 | Proxmox LXC | `git.andrims.net` | No |
| [Docs site](docs-site.md) | 🟡 | Proxmox LXC | `docs.andrims.net` | No |

"Critical" = losing its data would hurt. These need a tested backup and restore runbook first.
