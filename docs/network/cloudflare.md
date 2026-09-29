# Cloudflare & andrims.net

The domain **`andrims.net`** is managed on Cloudflare.

| | |
|---|---|
| **Registrar** | Cloudflare (also the registrar, not just DNS) |
| **Renewal date** | Nov 22, 2028 |
| **Also registered** | `andrims.com`, also on Cloudflare, renews Nov 8, 2028 — ❓ purpose / whether it's used for anything yet |
| **Account login** | (Vaultwarden → "Cloudflare") ❓ entry name |
| **Dynamic DNS** | [DDNS updater](https://community-scripts.github.io/ProxmoxVE/) LXC — `PVE-7050` → **CT 109** (`ddns-updater`), `10.10.0.109`. Confirmed: uses the **Cloudflare provider script**, keeping **`aultmain.andrims.net`** (not `stream.andrims.net` — see note below) pointed at the home public IP. Config lives at `/opt/ddns-updater/data/config.json` on the CT — **edit that file before changing DNS provider/target settings**, per the [community-scripts instructions](https://community-scripts.github.io/ProxmoxVE/). |

!!! warning "Correction: it updates aultmain.andrims.net, not stream.andrims.net"
    Earlier docs assumed the DDNS updater kept `stream.andrims.net` current. Checking the actual config shows only one live entry, for **`aultmain.andrims.net`** — plus an unused leftover `namecheap` / `example.com` entry that looks like the script's default template, never actually configured. **Resolved:** `stream.andrims.net` is a **CNAME → `aultmain.andrims.net`** (checked in public DNS), so keeping `aultmain` current keeps `stream` current too.

## Public DNS records

| Name | Type | Value | Proxy status | Purpose |
|---|---|---|---|---|
| [stream.andrims.net](https://stream.andrims.net) | CNAME | `aultmain.andrims.net` | **DNS only** (grey cloud) | Jellyfin via NPM |
| [immich.andrims.net](https://immich.andrims.net) | CNAME (tunnel) | `<tunnel-id>.cfargotunnel.com` | **Proxied** (orange cloud, required for tunnels) | Immich via Cloudflare Tunnel |
| [vault.andrims.net](https://vault.andrims.net) | CNAME (tunnel) | `<tunnel-id>.cfargotunnel.com` | **Proxied** (orange cloud, required for tunnels) | Vaultwarden via Cloudflare Tunnel |
| [request.andrims.net](https://request.andrims.net) | CNAME (tunnel) | `<tunnel-id>.cfargotunnel.com` | **Proxied** (orange cloud, required for tunnels) | Seerr via Cloudflare Tunnel |
| [aultmain.andrims.net](https://aultmain.andrims.net) | A | Home public IP (kept current by ddns-updater, CT 109) | **DNS only** | Target of the `stream` CNAME — the one record holding the home IP |
| `andrims.net` (root) | ❓ | Cloudflare-proxied | **Proxied** | Not part of the homelab |
| ❓ others | | | | |

!!! note "Why stream is DNS-only"
    `stream.andrims.net` is **not proxied** through Cloudflare. Traffic goes straight to the home IP, and NPM handles TLS. (Cloudflare's proxy terms discourage streaming large amounts of video through it.)

## Home IP exposure

**Goal:** the home IP is published under **`stream.andrims.net` only** (via its CNAME to `aultmain.andrims.net`). Every other public service goes through the Cloudflare Tunnel, so its DNS answer is a Cloudflare address.

Checked in public DNS on 2026-09-28:

| Name | Resolves to |
|---|---|
| `stream.andrims.net` → `aultmain.andrims.net` | Home IP ✅ expected |
| `immich.`, `vault.`, `request.`, root `andrims.net` | Cloudflare addresses — home IP hidden ✅ |
| Internal names (`git.`, `docs.`, `pve-7050.`, …) | No public record ✅ |
| Random name (wildcard test) | No record — there's no `*.andrims.net` wildcard ✅ |

**Never write the actual IP in this repo.** It's public on GitHub.

!!! tip "Planned permanent fix: the edge VPS"
    The [edge VPS](edge-vps.md) plan moves the public entry point to a cloud VM that reaches the lab over Tailscale. Public DNS then holds the VM's IP instead of the home IP, and ports 80/443 at home get closed, which also removes the NPM exposure below. Until then, the steps below apply.

!!! warning "DNS isn't the only way in"
    Ports 80 and 443 forward to NPM, and NPM answers for **every** proxy host it has, internal ones included. Anyone who knows the home IP (it's public through `stream`) can ask NPM for `pve-7050.andrims.net`, `truenas.andrims.net` and so on by sending that hostname directly. Missing DNS records don't stop this. The hostnames are also listed in these public docs and in public certificate logs.

    **Fix:** in NPM, create an **Access List** that allows only `10.10.0.0/24` and the Tailscale range `100.64.0.0/10` and denies everything else. Attach it to every internal proxy host (everything except `stream.andrims.net`). See [Reverse proxy](reverse-proxy.md#gotchas).

## Cloudflare Tunnel

| | |
|---|---|
| **Tunnel name** | ❓ |
| **`cloudflared` runs on** | `PVE-7050` → **CT 102** (`cloudflared`), a separate LXC from the [community scripts](https://community-scripts.github.io/ProxmoxVE/) — `10.10.0.102` |
| **Public hostnames** | `immich.andrims.net` → Immich (`http://10.10.0.107:2283`)<br/>`vault.andrims.net` → Vaultwarden (`http://10.10.0.140:8000`)<br/>`request.andrims.net` → Seerr (`http://10.10.0.140:5055`)<br/>All three share this one tunnel on CT 102. |

The tunnel is an outbound connection from `cloudflared`, so none of Immich, Vaultwarden or Seerr needs a port forward, and none goes through NPM. **Different reasons, though:** Immich is tunneled to avoid setting up a second public path (it's planned to move off, see below); Vaultwarden is tunneled because its web vault needs HTTPS to function at all; Seerr is tunneled so it doesn't need a direct home-IP record (see [Home IP exposure](#home-ip-exposure)) — see [Vaultwarden → Gotchas](../services/vaultwarden.md#gotchas).

!!! warning "Planned: move Immich off the tunnel"
    The tunnel itself stays, since Vaultwarden and Seerr use it. Immich is planned to move to the same setup as Jellyfin (DNS-only record → home IP → NPM). Main reason: Cloudflare's proxy caps uploads at **100 MB per request on the free plan**, which can break large video uploads. See [Immich → Planned move](../services/immich.md#planned-move-from-cloudflare-tunnel-to-direct-ip-npm).

## Deliberately *not* public

`git.andrims.net` and `docs.andrims.net` have **no** Cloudflare records. They exist only as [AdGuard rewrites](dns-adguard.md). The docs describe the whole network, so they stay off the internet.

## Gotchas

- `/opt/ddns-updater/data/config.json` (on **CT 109**) holds a live Cloudflare API token and zone ID in plaintext. **Never copy its contents into this repo** — see [Roadmap rule 5](../roadmap.md#standing-rules): no secrets here, reference Vaultwarden instead.
