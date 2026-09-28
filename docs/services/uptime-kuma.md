# Uptime Kuma (planned)

> **What / why:** Detects *that* something is down and sends a notification. [Dozzle](dozzle.md) then tells you *why*.

**Status:** 🟡 planned

## Where

- [Monitoring Pi](../hardware/monitoring-pi.md)

## Checks to set up

| Target | Check type |
|---|---|
| Vaultwarden | HTTP |
| Sonarr, Radarr, Prowlarr | HTTP |
| Jellyfin (`stream.andrims.net`) | HTTP (external) |
| Immich, Paperless-ngx | HTTP |
| AdGuard Home | DNS |
| Nginx Proxy Manager | HTTP |
| Forgejo, docs site (once deployed) | HTTP |
| Proxmox host, GF65, Pi | Ping |

## Notifications

❓ Which channel (Discord, ntfy, email…)?
