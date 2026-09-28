# Cloudflare & andrims.net

The domain **`andrims.net`** is managed on Cloudflare.

| | |
|---|---|
| **Registrar** | ❓ |
| **Renewal date** | ❓ |
| **Account login** | (Vaultwarden → "Cloudflare") ❓ entry name |
| **Dynamic DNS** | [DDNS updater](https://community-scripts.github.io/ProxmoxVE/) LXC — `PVE-7050` → **CT 109** (`ddns-updater`), `10.10.0.109` *(inferred — confirm)*. ❓ Which provider script / config (needs a Cloudflare API token — check it's scoped to just the DNS zone). |

## Public DNS records

| Name | Type | Value | Proxy status | Purpose |
|---|---|---|---|---|
| [stream.andrims.net](https://stream.andrims.net) | A ❓ | Home public IP | **DNS only** (grey cloud) | Jellyfin via NPM |
| [immich.andrims.net](https://immich.andrims.net) | CNAME (tunnel) | `<tunnel-id>.cfargotunnel.com` | **Proxied** (orange cloud, required for tunnels) | Immich via Cloudflare Tunnel |
| [vault.andrims.net](https://vault.andrims.net) | CNAME (tunnel) | `<tunnel-id>.cfargotunnel.com` | **Proxied** (orange cloud, required for tunnels) | Vaultwarden via Cloudflare Tunnel |
| ❓ others | | | | |

!!! note "Why stream is DNS-only"
    `stream.andrims.net` is **not proxied** through Cloudflare. Traffic goes straight to the home IP, and NPM handles TLS. (Cloudflare's proxy terms discourage streaming large amounts of video through it.)

## Cloudflare Tunnel

| | |
|---|---|
| **Tunnel name** | ❓ |
| **`cloudflared` runs on** | `PVE-7050` → **CT 102** (`cloudflared`), a separate LXC from the [community scripts](https://community-scripts.github.io/ProxmoxVE/) — `10.10.0.102` *(inferred — confirm)* |
| **Public hostnames** | `immich.andrims.net` → Immich (`http://10.10.0.107:2283`)<br/>`vault.andrims.net` → Vaultwarden (`http://10.10.0.140:8000`) — ❓ confirm both hostnames run through this same tunnel/CT rather than two separate ones |

The tunnel is an outbound connection from `cloudflared`, so neither Immich nor Vaultwarden needs a port forward, and neither goes through NPM. **Different reasons, though:** Immich is tunneled to avoid setting up a second public path (it's planned to move off, see below); Vaultwarden is tunneled because its web vault needs HTTPS to function at all — see [Vaultwarden → Gotchas](../services/vaultwarden.md#gotchas).

!!! warning "Planned: retire the tunnel"
    Immich is planned to move to the same setup as Jellyfin (DNS-only record → home IP → NPM). Main reason: Cloudflare's proxy caps uploads at **100 MB per request on the free plan**, which can break large video uploads. See [Immich → Planned move](../services/immich.md#planned-move-from-cloudflare-tunnel-to-direct-ip-npm).

## Deliberately *not* public

`git.andrims.net` and `docs.andrims.net` have **no** Cloudflare records. They exist only as [AdGuard rewrites](dns-adguard.md). The docs describe the whole network, so they stay off the internet.
