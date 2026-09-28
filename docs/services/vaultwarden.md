# Vaultwarden

> **What / why:** Self-hosted, Bitwarden-compatible password manager. Also the place every credential referenced in these docs lives.

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | [MSI GF65](../hardware/msi-gf65.md) — Windows 10 Pro, Docker Desktop |
| **Type** | Docker container |
| **IP / ports** | ❓ |

## How it was deployed

❓ Paste the compose file (with secrets removed):

```yaml
# docker-compose.yml
```

## Access

| | |
|---|---|
| **URL** | ❓ |
| **NPM proxy host** | ❓ |
| **Internal / external** | ❓ |
| **Admin page** | ❓ `/admin` enabled? token stored where? |

## Data

- Data folder on the GF65: ❓ path (bind mount or Docker volume?)

## Backups

!!! danger "Highest priority gap"
    This is the most important data in the lab, and it sits on a Windows 10 laptop that no longer gets security updates. Back up the data folder **off that laptop** (e.g. to TrueNAS) and write a restore runbook.

- What's backed up: ❓
- Where to: ❓
- How to restore: ❓ → `runbooks/restore-vaultwarden.md` (to be written)

## Updates

❓

## Monitoring

- Planned: Uptime Kuma HTTP check; Dozzle agent on the GF65.

## Gotchas

- Docker Desktop only starts after a Windows login — Vaultwarden is down after a reboot until someone signs in (❓ unless auto-login is set).
- Long term: migrate off Windows along with the rest of the GF65. See [Roadmap](../roadmap.md).
