# Vaultwarden

> **What / why:** Self-hosted, Bitwarden-compatible password manager. Also the place every credential referenced in these docs lives.

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | `Server-GF65` ([MSI GF65](../hardware/msi-gf65.md)) — Windows 10 Pro, Docker Desktop |
| **Type** | Docker container |
| **LAN IP / port** | `10.10.0.140` : 8000 |

## How it was deployed

❓ Paste the compose file (with secrets removed):

```yaml
# docker-compose.yml
```

## Access

| | |
|---|---|
| **Domain URL** | [vault.andrims.net](https://vault.andrims.net) |
| **LAN URL** | [http://10.10.0.140:8000](http://10.10.0.140:8000) — loads, but the vault itself won't fully work over plain HTTP (see Gotchas) |
| **Public?** | **Yes** |
| **Path today** | Cloudflare Tunnel (`cloudflared`, CT 102) → Vaultwarden. **Not** via NPM. |
| **NPM proxy host** | None — see Path above |
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

- **Why this is public:** the web vault uses WebCrypto for encryption, which browsers only allow in a secure context (HTTPS or `localhost`) — hit the plain LAN URL and the page loads but core functions (unlock, autofill) silently fail. Rather than set up an internal cert, it's exposed via Cloudflare Tunnel instead, the same mechanism as [Immich](immich.md).
- **Worth considering:** an AdGuard rewrite + NPM certificate (like [Paperless-ngx](paperless-ngx.md)) would give it working HTTPS *without* putting a password manager on the public internet — an option if that tradeoff seems worth it.
- **Now publicly reachable** at `vault.andrims.net` — worth strong master passwords / 2FA on every account, and checking whether the admin panel needs extra protection (IP allowlist, disabling it entirely if unused), since it's a password vault facing the internet.
- Docker Desktop only starts after a Windows login — Vaultwarden is down after a reboot until someone signs in (❓ unless auto-login is set).
- Long term: migrate off Windows along with the rest of the GF65. See [Roadmap](../roadmap.md).
