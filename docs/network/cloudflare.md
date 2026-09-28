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
| `stream.andrims.net` | A ❓ | Home public IP | **DNS only** (grey cloud) | Jellyfin via NPM |
| ❓ others | | | | |

!!! note "Why stream is DNS-only"
    `stream.andrims.net` is **not proxied** through Cloudflare. Traffic goes straight to the home IP, and NPM handles TLS. (Cloudflare's proxy terms discourage streaming large amounts of video through it.)

## Deliberately *not* public

`git.andrims.net` and `docs.andrims.net` have **no** Cloudflare records. They exist only as [AdGuard rewrites](dns-adguard.md). The docs describe the whole network, so they stay off the internet.
