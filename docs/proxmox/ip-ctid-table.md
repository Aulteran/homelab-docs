# IP / CTID table

All guests run on **`PVE-7050`** (`10.10.0.15`). **One row per guest.** Update this in the same commit as any change to the lab.

Last checked against `pct list` / `qm list` / `pct config` on the host: **2026-09-28**.

!!! note "IP convention"
    Most guests get `10.10.0.<CTID last 3 digits>` (e.g. CT 106 → `.106`). **AdGuard (CT 153 → `.53`) and TrueNAS (VM 104 → `.22`) are exceptions** — don't assume the pattern for those two.

## LXC containers

| CTID | Hostname | IP | Deployed via | Runs | Cores / RAM / disk |
|---|---|---|---|---|---|
| 101 | `nginxproxymanager` | `10.10.0.101` | ❓ Helper script? | [Nginx Proxy Manager](../network/reverse-proxy.md) | 2 / 2 GB / 10 GB |
| 102 | `cloudflared` | `10.10.0.102` ⚠️ | Helper script | Cloudflare Tunnel daemon for [Immich](../services/immich.md), [Vaultwarden](../services/vaultwarden.md) and Seerr — ❓ confirm all three share this one instance | 1 / 512 MB / 2 GB |
| 105 | `docker` | `10.10.0.105` | ❓ Helper script / manual | **Docker host** for lightweight Compose stacks: the [docs site](../services/docs-site.md) nginx container, and ❓ [Paperless-ngx](../services/paperless-ngx.md) (this CT used to be the standalone `paperless` LXC — confirm Paperless now runs here as a container) | 2 / 2 GB / 32 GB |
| 106 | `forgejo` | `10.10.0.106` | Helper script | [Forgejo](../services/forgejo.md) | 1 / 512 MB / 6 GB |
| 109 | `ddns-updater` | `10.10.0.109` | Helper script | Keeps `aultmain.andrims.net`'s A record pointed at the home IP (config: `/opt/ddns-updater/data/config.json`) — see [Cloudflare](../network/cloudflare.md) | 1 / 512 MB / 2 GB |
| 110 | `actualbudget` | `10.10.0.110` | Helper script | [ActualBudget](../services/actualbudget.md) — personal finance | 2 / 2 GB / 4 GB |
| 153 | `adguard-alpine` | `10.10.0.53` ⚠️ | ❓ Helper script (Alpine variant)? | [AdGuard Home](../network/dns-adguard.md) | 1 / 128 MB / 10 GB |

⚠️ `pct config` showed **no `net0` line** for CT 102 and CT 153, so their IPs come from earlier confirmation, not from the config dump. Check which interface they use with `pct config 102 | grep ^net` (and the same for 153).

## VMs

| VMID | Name | IP | Status | Runs | RAM / boot disk |
|---|---|---|---|---|---|
| 104 | `truenas` | `10.10.0.22` | ✅ running | [TrueNAS](../services/truenas.md) — 4 TB SATA SSD passed through | 10 GB / 16 GB |
| 107 | `debian-immich` | `10.10.0.107` | ✅ running | [Immich](../services/immich.md) on Debian | 7 GB / 20 GB |
| 171 | `haos` | ❓ | ⏸️ **stopped** | Home Assistant OS — ❓ not in use yet; no service page | 2 GB / 32 GB |

VM core counts aren't shown by `qm list` — get them with `qm config <vmid> | grep -E '^(cores|sockets|memory|net0)'`.

## Removed

CT 100, 103 and 108 (old one-off / Docker-in-LXC containers) have been **deleted**. The old standalone `paperless` LXC is gone; CT 105 is now the `docker` host.

!!! tip "Re-checking this table"
    On the Proxmox host:
    ```bash
    pct list          # LXCs
    qm list           # VMs
    for id in $(pct list | awk 'NR>1{print $1}'); do echo "== $id"; pct config $id | grep -E '^(hostname|net[0-9]|cores|memory|rootfs)'; done
    for id in $(qm list | awk 'NR>1{print $1}'); do echo "== $id"; qm config $id | grep -E '^(name|net[0-9]|cores|sockets|memory)'; done
    ```

## Machines outside Proxmox

| Machine | Hostname | LAN IP | Runs |
|---|---|---|---|
| [Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md) (Proxmox host) | `PVE-7050` | `10.10.0.15` | Proxmox VE (guests above) |
| [MSI GF65](../hardware/msi-gf65.md) | `Server-GF65` | `10.10.0.140` | Jellyfin, Seerr, Radarr, Sonarr, Prowlarr, Bazarr, qBittorrent, Vaultwarden |
| [Monitoring Pi](../hardware/monitoring-pi.md) | `raspberrypi` | `10.10.0.6` | Glance, Dozzle |
| [MSI Raider GE68 HX](../hardware/msi-raider-ge68hx.md) | — (client) | DHCP | Nothing (client) |
| [M1 MacBook Air](../hardware/macbook-air.md) | — (client) | DHCP | Nothing (client) |
