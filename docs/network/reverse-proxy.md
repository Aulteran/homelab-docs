# Reverse proxy — Nginx Proxy Manager

NPM receives incoming HTTPS and routes it to internal services. It's also the front door for internal-only hostnames via [AdGuard rewrites](dns-adguard.md).

| | |
|---|---|
| **Host** | ❓ (LXC? Docker in CT 105/108?) |
| **IP** | ❓ |
| **Admin UI** | ❓ (port 81 by default) |
| **Login** | (Vaultwarden → "Nginx Proxy Manager") ❓ entry name |
| **Certificates** | ❓ Let's Encrypt — HTTP challenge or Cloudflare DNS challenge? |

## Proxy hosts

| Domain | Forward to | Public? | SSL | Status |
|---|---|---|---|---|
| `stream.andrims.net` | Jellyfin on MSI GF65 — ❓ IP:8096 | **Yes** | ❓ | ✅ |
| `git.andrims.net` | Forgejo LXC — ❓ IP:3000 | No | ❓ | 🟡 |
| `docs.andrims.net` | docs LXC — ❓ IP:80 | No | ❓ | 🟡 |
| ❓ others (Vaultwarden? Immich? Paperless?) | | | | |

## Gotchas

- ❓ Access lists used to restrict internal hosts?
- ❓ Websockets enabled for Jellyfin / Vaultwarden?
