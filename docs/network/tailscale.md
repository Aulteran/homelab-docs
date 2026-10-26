# Tailscale

Tailscale mesh networking is the remote-access path into the lab. Internal-only services (Forgejo, docs site, monitoring) are reached over Tailscale rather than exposed publicly.

| | |
|---|---|
| **Tailnet DNS name** | `chocolate-wall.ts.net` (also the search domain) |
| **Admin login** | GitHub, through the GitHub organization `AndrimsDevs` |
| **MagicDNS** | Enabled |
| **Tailnet DNS** | Default only. No custom nameservers, no split DNS; devices use `100.100.100.100`. AdGuard is **not** set as a tailnet nameserver. |
| **ACLs** | None configured. The default policy applies, so almost every device can reach every other device and service — **including devices owned by the second tailnet user, `Bowswa@`** (see the device list). |
| **Users** | `Aulteran@` (Aadil) and `Bowswa@`. Servers are tagged devices (`tagged-devices`). |
| **App connector** | Yes — routes all `*.andrims.net` traffic from tailnet devices to the lab (see below) |
| **Where Tailscale runs** | Installed **directly on the PVE-7050 host** (`10.10.0.15`), not in an LXC or VM. The app connector runs there too. |
| **Subnet router** | PVE-7050 advertises `10.10.0.0/24`, but the route is **disabled in the Tailscale admin console**. Turn it on only when the whole home network is needed from outside, then turn it off again. |
| **Exit node** | [Server-GF65](../hardware/msi-gf65.md) is set up as an exit node, but it's rarely if ever used |

!!! info "About `chocolate-wall.ts.net`"
    This is the tailnet's MagicDNS suffix. Tailnet devices resolve as `<device>.chocolate-wall.ts.net`. It is not a credential and doesn't let anyone in, but it does identify the tailnet, so keep this repo and the docs site private rather than publishing them.

## How remote access works: app connector

Any tailnet device that visits an `*.andrims.net` name is sent through a Tailscale **app connector**. The connector runs on **PVE-7050**, and from there the traffic behaves exactly like a device sitting on the home LAN:

1. Tailnet device opens `something.andrims.net`.
2. The app connector matches the `*.andrims.net` domain and routes the traffic to PVE-7050 over Tailscale.
3. PVE-7050 resolves the name with its own resolver (see the DNS gotcha below). [AdGuard Home](dns-adguard.md) answers, and its rewrite points the name at NPM.
4. [Nginx Proxy Manager](reverse-proxy.md) receives the request and serves the service.

```
tailnet device → app connector on PVE-7050 → LAN → AdGuard (DNS) → NPM → service
```

What this means in practice:

- The same `*.andrims.net` names and internal-only services work at home and away, with no separate remote hostnames.
- Internal services are never exposed publicly for this. The public paths are only the four in [Network](index.md#how-traffic-flows).
- **PVE-7050 is a single point of failure for remote access.** If the host is down, nothing here works from outside, and it takes NPM and AdGuard down with it anyway.
- Traffic arrives at NPM from the PVE-7050 host (`10.10.0.15`) or its Tailscale address rather than each client's own IP, so NPM Access Lists must allow that source. See the [NPM Access List item](../open-questions.md).

## Gotchas

- **`--accept-dns=false` on PVE-7050.** Tailscale on the host is brought up with `--accept-dns=false`, because accepting tailnet DNS kept breaking DNS for all the LXCs. Keep it that way, and include the flag whenever re-running `tailscale up` (a bare `tailscale up` can reset flags). Because of it, the host keeps its own DNS settings, and those are what resolve `*.andrims.net` for the app connector. The host's resolver points at AdGuard (`10.10.0.53`) — to re-check, run `cat /etc/resolv.conf` on the host.
- **Tailscale runs on the Proxmox host itself.** That breaks the usual rule of keeping the host bare, and it is a deliberate exception. Note it before host upgrades or a rebuild, since Tailscale has to be reinstalled and re-authenticated by hand.
- **No ACLs.** With the default allow-all policy, every tailnet device can reach the Proxmox UI and everything else on the host. That includes the other user's three devices, and PVE-7050 and VM 107 accept **root SSH with a password**. This also matters for the planned [edge VPS](edge-vps.md), which must get a restrictive ACL before it joins.
- **Stale devices.** `oracle1`, `oracle2` and `ltbh-f15` haven't been seen for 97–348 days. Remove them in the admin console if they're gone for good.

## Devices on the tailnet

From `tailscale status` on PVE-7050, 2026-10-25.

| Device | Tailscale IP | Owner | Notes |
|---|---|---|---|
| `pve-7050` | `100.110.0.15` | tagged | Proxmox host, app connector, subnet router (disabled) |
| `server-gf65` | `100.69.160.6` | tagged | Exit node (rarely used); always on |
| `monitoring-pi` | `100.101.228.37` | tagged | |
| `truenas-scale` | `100.110.0.22` | tagged | [TrueNAS](../services/truenas.md) (VM 104) |
| `vm-107` | `100.110.0.107` | tagged | [Immich](../services/immich.md) VM |
| `ct-105` | `100.85.137.111` | tagged | `docker` CT 105 (it has `/dev/net/tun` passed in) |
| `oracle1` | `100.117.240.65` | tagged | Offline 97 days — ❓ old Oracle VPS test? |
| `oracle2` | `100.121.48.9` | tagged | Offline 110 days |
| `ault-ge68` | `100.101.97.6` | Aulteran@ | [MSI Raider GE68 HX](../hardware/msi-raider-ge68hx.md) workstation |
| `aadils-m1-macbook-air` | `100.95.170.40` | Aulteran@ | [M1 MacBook Air](../hardware/macbook-air.md) |
| `aadil-s24` | `100.65.156.66` | Aulteran@ | Android phone |
| `ltbh-f15` | `100.73.191.112` | Aulteran@ | Windows, offline 348 days |
| `furnace` | `100.95.22.125` | Bowswa@ | Windows, offers an exit node |
| `lootcrate` | `100.111.190.90` | Bowswa@ | Windows |
| `iphone182` | `100.112.178.100` | Bowswa@ | iOS |

## Planned use

- **Edge VPS:** a cloud VM joins the tailnet as the public entry point and forwards to the GF65 over Tailscale. Give it a restrictive ACL (only the ports it proxies). See [Edge VPS](edge-vps.md).
- [Dozzle](../services/dozzle.md): point the main instance at agents' **Tailscale IPs** instead of opening port 7007 on the LAN (especially the Windows GF65).
