# Service name

> **What / why:** one line on what this is and why it's in the lab.

**Status:** ✅ running · 🟡 planned · ❌ retired

## Where

| | |
|---|---|
| **Host** | e.g. Proxmox / MSI GF65 / Monitoring Pi |
| **Type + ID** | LXC 1xx / VM 1xx / Docker container |
| **LAN IP** | |
| **Ports** | |

## How it was deployed

Helper script name, or the compose file:

```yaml
# docker-compose.yml
```

## Access

| | |
|---|---|
| **Domain URL** | [service.andrims.net](https://service.andrims.net) |
| **LAN URL** | [http://192.168.x.x:port](http://192.168.x.x:port) — direct to the host, for when NPM or DNS is down |
| **NPM proxy host** | |
| **Internal / external** | |
| **Login** | (Vaultwarden → "entry name") |

## Data

Volumes and mount paths, and where they physically live.

## Backups

- **What's backed up:**
- **Where to:**
- **How to restore:** (link a runbook)

## Updates

The exact command or process.

## Monitoring

Glance widget? Uptime Kuma check? Dozzle agent?

## Gotchas

Anything you had to figure out the hard way.
