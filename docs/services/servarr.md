# Servarr stack

> **What / why:** Media automation — finds and organizes movies and TV for [Jellyfin](jellyfin.md).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | `Server-GF65` ([MSI GF65](../hardware/msi-gf65.md), `10.10.0.140`) — Docker Desktop |
| **Compose** | ❓ one stack or separate? path on disk? |

## Apps

| App | Purpose | Port | Domain URL | LAN URL |
|---|---|---|---|---|
| Radarr | Movies | ❓ (7878) | [servarr.andrims.net/radarr](https://servarr.andrims.net/radarr) | [http://10.10.0.140:7878](http://10.10.0.140:7878) |
| Sonarr | TV | ❓ (8989) | [servarr.andrims.net/sonarr](https://servarr.andrims.net/sonarr) | [http://10.10.0.140:8989](http://10.10.0.140:8989) |
| Prowlarr | Indexer manager | ❓ (9696) | [servarr.andrims.net/prowlarr](https://servarr.andrims.net/prowlarr) | [http://10.10.0.140:9696](http://10.10.0.140:9696) |
| Bazarr | Subtitles | ❓ (6767) | [servarr.andrims.net/bazarr](https://servarr.andrims.net/bazarr) | [http://10.10.0.140:6767](http://10.10.0.140:6767) |
| qBittorrent | Download client | 8081 | [servarr.andrims.net](https://servarr.andrims.net) (root) | [http://10.10.0.140:8081](http://10.10.0.140:8081) |
| Seerr (formerly Jellyseerr) | Media requests for Jellyfin — **public** via Cloudflare Tunnel | ❓ (5055) | [request.andrims.net](https://request.andrims.net) | [http://10.10.0.140:5055](http://10.10.0.140:5055) |

## How it was deployed

❓ Paste the compose file (API keys removed):

```yaml
# docker-compose.yml
```

## Data

- ❓ Download folder, media library root folders, config volumes.

## Backups

- ❓ Each *arr app has built-in scheduled backups — where do they land?

## Monitoring

- Planned: Uptime Kuma HTTP checks on Radarr, Sonarr, Prowlarr, Bazarr and qBittorrent; Dozzle agent on the GF65 for logs.

## Gotchas

- **Path-based routing.** Everything shares `servarr.andrims.net`: qBittorrent at the root, each *arr app under its own path. That only works if each app's **URL Base** (Settings → General) matches its path (`/radarr`, `/sonarr`, `/prowlarr`, `/bazarr`). Don't clear those settings, or the domain links break.
