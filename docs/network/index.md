# Network

| Layer | What | Page |
|---|---|---|
| Physical / LAN | UniFi hardware | [UniFi](unifi.md) |
| Internal DNS | AdGuard Home (custom DNS + rewrites) | [DNS](dns-adguard.md) |
| Reverse proxy | Nginx Proxy Manager | [Reverse proxy](reverse-proxy.md) |
| Public DNS | Cloudflare, `andrims.net` | [Cloudflare](cloudflare.md) |
| Remote access | Tailscale mesh | [Tailscale](tailscale.md) |

## Subnets / VLANs

| Name | Subnet | VLAN ID | Purpose |
|---|---|---|---|
| ❓ LAN | ❓ | ❓ | ❓ |

## How traffic flows

**Public (only Jellyfin today):**
Internet → Cloudflare DNS (`stream.andrims.net`, DNS-only) → home public IP → UniFi port-forward 443 ❓ → Nginx Proxy Manager → Jellyfin on the MSI GF65.

**Internal-only services (`git.`, `docs.`, …):**
Client → AdGuard Home rewrite (`*.andrims.net` → NPM IP) → Nginx Proxy Manager → service. No public Cloudflare record.

**Remote:**
Device on Tailscale → internal IPs / internal hostnames. ❓ Does Tailscale use AdGuard as its DNS (split DNS / global nameserver)?

## Port forwards

| External port | Internal target | For |
|---|---|---|
| 443 ❓ | NPM ❓ | stream.andrims.net |
| 80 ❓ | NPM ❓ | Let's Encrypt HTTP challenge? |
