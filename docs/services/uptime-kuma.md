# Uptime Kuma

> **What / why:** Detects *that* something is down and sends a notification. [Dozzle](dozzle.md) then tells you *why*.

**Status:** ✅ running at [uptime.andrims.net](https://uptime.andrims.net)

## Where

- [Monitoring Pi](../hardware/monitoring-pi.md), as a Docker container (`uptime-kuma-uptime-kuma-1`, Compose project `uptime-kuma`)

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

- **Discord:** enabled
  - Currently monitoring: **Jellyfin** only
  - To be added: remaining services (as time permits)

See Vaultwarden for the Discord webhook URL.
