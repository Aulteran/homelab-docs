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

**Public — Jellyfin (direct IP + NPM):**
Internet → Cloudflare DNS (`stream.andrims.net`, DNS-only) → home public IP → UniFi port-forward 443 ❓ → Nginx Proxy Manager → Jellyfin on the MSI GF65.

**Public — Immich (Cloudflare Tunnel):**
Internet → Cloudflare edge (`immich.andrims.net`, proxied) → Cloudflare Tunnel → `cloudflared` ❓ host → Immich VM. No port forward, no NPM. 🟡 Planned to move to the Jellyfin-style path above.

**Internal-only services (`git.`, `docs.`, …):**
Client → AdGuard Home rewrite (`*.andrims.net` → NPM IP) → Nginx Proxy Manager → service. No public Cloudflare record.

**Remote:**
Device on Tailscale → internal IPs / internal hostnames. ❓ Does Tailscale use AdGuard as its DNS (split DNS / global nameserver)?

## Port forwards

| External port | Internal target | For |
|---|---|---|
| 443 ❓ | NPM ❓ | stream.andrims.net |
| 80 ❓ | NPM ❓ | Let's Encrypt HTTP challenge? |
