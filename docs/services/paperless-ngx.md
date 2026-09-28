# Paperless-ngx

> **What / why:** Document management — scan, OCR and search paper documents.

**Status:** ❓ — the standalone `paperless` LXC (old CT 105) is gone; CT 105 is now the shared `docker` host. Confirm Paperless-ngx was migrated into a Compose stack there and is running.

## Where

| | |
|---|---|
| **Host** | `PVE-7050` → **CT 105** (`docker`) ❓ confirm |
| **Type + ID** | Docker Compose stack on LXC CT 105 ❓ |
| **LAN IP / port** | `10.10.0.105` : 8000 (default) ❓ |

## Access

| | |
|---|---|
| **Domain URL** | [paperless.andrims.net](https://paperless.andrims.net) |
| **LAN URL** | ❓ [http://10.10.0.105:8000](http://10.10.0.105:8000) |
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
