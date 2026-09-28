# Cloudflare & andrims.net

The domain **`andrims.net`** is managed on Cloudflare.

| | |
|---|---|
| **Registrar** | ❓ |
| **Renewal date** | ❓ |
| **Account login** | (Vaultwarden → "Cloudflare") ❓ entry name |
| **Dynamic DNS** | ❓ How is the home IP kept current on `stream.`? (DDNS container, UniFi, manual?) |

## Public DNS records

| Name | Type | Value | Proxy status | Purpose |
|---|---|---|---|---|
| [stream.andrims.net](https://stream.andrims.net) | A ❓ | Home public IP | **DNS only** (grey cloud) | Jellyfin via NPM |
| [immich.andrims.net](https://immich.andrims.net) | CNAME (tunnel) | `<tunnel-id>.cfargotunnel.com` | **Proxied** (orange cloud, required for tunnels) | Immich via Cloudflare Tunnel |
| ❓ others | | | | |

!!! note "Why stream is DNS-only"
    `stream.andrims.net` is **not proxied** through Cloudflare. Traffic goes straight to the home IP, and NPM handles TLS. (Cloudflare's proxy terms discourage streaming large amounts of video through it.)

## Cloudflare Tunnel

| | |
|---|---|
| **Tunnel name** | ❓ |
| **`cloudflared` runs on** | ❓ (inside the Immich VM? separate LXC? Docker?) |
| **Public hostnames** | `immich.andrims.net` → Immich (❓ `http://<LAN IP>:2283`) |

The tunnel is an outbound connection from `cloudflared`, so Immich needs no port forward and doesn't go through NPM.

!!! warning "Planned: retire the tunnel"
    Immich is planned to move to the same setup as Jellyfin (DNS-only record → home IP → NPM). Main reason: Cloudflare's proxy caps uploads at **100 MB per request on the free plan**, which can break large video uploads. See [Immich → Planned move](../services/immich.md#planned-move-from-cloudflare-tunnel-to-direct-ip-npm).

## Deliberately *not* public

`git.andrims.net` and `docs.andrims.net` have **no** Cloudflare records. They exist only as [AdGuard rewrites](dns-adguard.md). The docs describe the whole network, so they stay off the internet.
