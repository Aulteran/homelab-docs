# Servarr stack

> **What / why:** Media automation — finds and organizes movies and TV for [Jellyfin](jellyfin.md).

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | [MSI GF65](../hardware/msi-gf65.md) — Docker Desktop |
| **Compose** | ❓ one stack or separate? path on disk? |

## Apps

| App | Purpose | Port | URL |
|---|---|---|---|
| Radarr | Movies | ❓ (7878) | ❓ |
| Sonarr | TV | ❓ (8989) | ❓ |
| Prowlarr | Indexer manager | ❓ (9696) | ❓ |
| ❓ download client | | | |
| ❓ others (Bazarr, Jellyseerr…) | | | |

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

- Planned: Uptime Kuma HTTP checks on Sonarr, Radarr and Prowlarr; Dozzle agent on the GF65 for logs.

## Gotchas

❓
