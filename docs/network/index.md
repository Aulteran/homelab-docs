# Network

| Layer | What | Page |
|---|---|---|
| Physical / LAN | UniFi hardware | [UniFi](unifi.md) |
| Internal DNS | AdGuard Home (custom DNS + rewrites) | [DNS](dns-adguard.md) |
| Reverse proxy | Nginx Proxy Manager | [Reverse proxy](reverse-proxy.md) |
| Public DNS | Cloudflare, `andrims.net` | [Cloudflare](cloudflare.md) |
| Remote access | Tailscale mesh | [Tailscale](tailscale.md) |
| Public entry point (planned) | Cloud VM running NPM, on the tailnet | [Edge VPS](edge-vps.md) |

## Subnets / VLANs

| Name | Subnet | VLAN ID | Purpose |
|---|---|---|---|
| ❓ LAN | ❓ | ❓ | ❓ |

## How traffic flows

**Public — Jellyfin (direct IP + NPM):**
Internet → Cloudflare DNS (`stream.andrims.net`, CNAME → `aultmain.andrims.net`, DNS-only) → home public IP → UniFi (UX7) port-forward 443 → Nginx Proxy Manager → Jellyfin on the MSI GF65.

🟡 **Planned:** Jellyfin's path moves to an [edge VPS](edge-vps.md) (cloud NPM → Tailscale → GF65) so the home IP is no longer published and these port forwards go away.

**Public — Immich (Cloudflare Tunnel):**
Internet → Cloudflare edge (`immich.andrims.net`, proxied) → Cloudflare Tunnel → `cloudflared` (CT 102) → Immich VM. No port forward, no NPM. 🟡 Planned to move to the Jellyfin-style path above.

**Public — Vaultwarden and Seerr (Cloudflare Tunnel):**
Internet → Cloudflare edge (`vault.andrims.net` / `request.andrims.net`, proxied) → Cloudflare Tunnel → `cloudflared` (CT 102) → Vaultwarden / Seerr on the MSI GF65. No port forward, no NPM, home IP stays hidden.

**Internal-only services (`git.`, `docs.`, …):**
Client → AdGuard Home rewrite (`*.andrims.net` → NPM IP) → Nginx Proxy Manager → service. No public Cloudflare record.

**Remote:**
Device on Tailscale → internal IPs / internal hostnames. ❓ Does Tailscale use AdGuard as its DNS (split DNS / global nameserver)?

## Port forwards

| External port | Internal target | For |
|---|---|---|
| 443 | NPM — `10.10.0.101` (CT 101) | stream.andrims.net, aultmain.andrims.net |
| 80 | NPM — `10.10.0.101` (CT 101) | HTTP → HTTPS redirect / Let's Encrypt challenge |

Confirmed by Aadil: both 80 and 443 are forwarded straight from the UX7 to NPM (CT 101).
