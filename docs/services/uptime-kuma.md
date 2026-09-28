# Uptime Kuma (planned)

> **What / why:** Detects *that* something is down and sends a notification. [Dozzle](dozzle.md) then tells you *why*.

**Status:** 🟡 planned ❓ (domain [uptime.andrims.net](https://uptime.andrims.net) exists in NPM — running already?)

## Where

- [Monitoring Pi](../hardware/monitoring-pi.md)

## Checks to set up

| Target | Check type |
|---|---|
| Vaultwarden | HTTP |
| Radarr, Sonarr, Prowlarr, Bazarr, qBittorrent | HTTP |
| Jellyfin (`stream.andrims.net`) | HTTP (external) |
| Immich (`immich.andrims.net`) | HTTP (external) + HTTP (LAN) |
| Paperless-ngx | HTTP |
| AdGuard Home | DNS |
| Nginx Proxy Manager | HTTP |
| Forgejo, docs site (once deployed) | HTTP |
| Proxmox host, GF65, Pi | Ping |

Where a service has both a domain and a LAN URL, check **both**. If only the domain check fails, the problem is in front of the service (NPM, DNS, tunnel), not the service itself.

## Notifications

❓ Which channel (Discord, ntfy, email…)?
