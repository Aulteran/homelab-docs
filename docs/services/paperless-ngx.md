# Paperless-ngx

> **What / why:** Document management — scan, OCR and search paper documents.

**Status:** ✅ running on CT 105, the shared `docker` host (the old standalone `paperless` LXC is gone).

## Where

| | |
|---|---|
| **Host** | **CT 105** (`docker`) |
| **Type + ID** | Docker Compose project **`paperless-ault`** on LXC CT 105 (`docker`): `webserver` (`ghcr.io/paperless-ngx/paperless-ngx:latest`), `db` (`postgres:17`), `broker` (`redis:8`), `gotenberg` (`gotenberg/gotenberg:8.22`) and `tika` (`apache/tika:latest`). All `restart: unless-stopped`. No named Docker volumes, so data is in bind mounts — ❓ paths. |
| **LAN IP / port** | `10.10.0.105` : 8001 (not the default 8000) |

## Access

| | |
|---|---|
| **Domain URL** | [paperless.andrims.net](https://paperless.andrims.net) |
| **LAN URL** | [http://10.10.0.105:8001](http://10.10.0.105:8001) |
| **NPM proxy host** | ❓ |
| **Login** | (Vaultwarden → "Paperless") ❓ |

## Data

- ❓ media / data / consume folder paths
- ❓ Do originals live on TrueNAS?

## Backups

- ❓ `document_exporter` scheduled? Where does the export go?

## Updates

❓

## Gotchas

❓
