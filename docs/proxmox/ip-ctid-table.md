# IP / CTID table

All guests run on **`PVE-7050`** (`10.10.0.15`). **One row per guest.** Update this in the same commit as any change to the lab.

Last checked against `pct list` / `qm list` / `pct config` / `qm config` on the host: **2026-10-25**.

!!! note "IP convention"
    Most guests get `10.10.0.<CTID last 3 digits>` (e.g. CT 106 → `.106`). **AdGuard (CT 153 → `.53`) and TrueNAS (VM 104 → `.22`) are exceptions** — don't assume the pattern for those two.

## LXC containers

| CTID | Hostname | IP | OS | Deployed via | Runs | Cores / RAM / disk |
|---|---|---|---|---|---|---|
| 101 | `nginxproxymanager` | `10.10.0.101` | Debian 12 | Helper script | [Nginx Proxy Manager](../network/reverse-proxy.md) | 2 / 2 GB / 10 GB |
| 102 | `cloudflared` | `10.10.0.102` (on `net1`) | Debian 13 | Helper script | Cloudflare Tunnel daemon for [Immich](../services/immich.md), [Vaultwarden](../services/vaultwarden.md) and Seerr — one tunnel for all three | 1 / 512 MB / 2 GB |
| 105 | `docker` | `10.10.0.105` | Debian 13 | Helper script | **Docker host** for lightweight Compose stacks: the [docs site](../services/docs-site.md) nginx container, [Paperless-ngx](../services/paperless-ngx.md) on port 8001 (this CT used to be the standalone `paperless` LXC), `dozzle-agent` and `portainer_agent`. On the tailnet as `ct-105`. | 2 / 2 GB / 32 GB |
| 106 | `forgejo` | `10.10.0.106` | Alpine 3.24 | Helper script | [Forgejo](../services/forgejo.md) | 1 / 512 MB / 6 GB |
| 109 | `ddns-updater` | `10.10.0.109` | Debian 13 | Helper script | Keeps `aultmain.andrims.net`'s A record pointed at the home IP (config: `/opt/ddns-updater/data/config.json`) — see [Cloudflare](../network/cloudflare.md) | 1 / 512 MB / 2 GB |
| 110 | `actualbudget` | `10.10.0.110` | Debian 13 | Helper script | [ActualBudget](../services/actualbudget.md) — personal finance | 2 / 2 GB / 4 GB |
| 153 | `adguard-alpine` | `10.10.0.53` (on `net1`) | Alpine 3.23 | Helper script (Alpine variant) | [AdGuard Home](../network/dns-adguard.md) | 1 / 128 MB / 10 GB |

CT 102 and CT 153 have their network on **`net1`**, not `net0`, which is why an earlier `grep net0` missed them. Both have the Proxmox firewall flag set on the interface (`firewall=1`), and CT 102 also has `tag=1` (VLAN 1). The datacenter firewall is off, so neither setting does anything today.

Pending package updates on 2026-10-25: CT 101 **77**, CT 105 **76**, CT 110 **74**; the others 0–1.

## VMs

| VMID | Name | IP | Status | Runs | vCPUs / RAM / disks | Passthrough |
|---|---|---|---|---|---|---|
| 104 | `truenas` | `10.10.0.22` | ✅ running | [TrueNAS](../services/truenas.md) SCALE — 4 TB SATA SSD | 2 / 10 GB / 16 GB boot | SATA controller `00:17` |
| 107 | `debian-immich` | `10.10.0.107` | ✅ running | [Immich](../services/immich.md) on Debian 12 | 6 (3 sockets × 2 cores) / 7 GB (`balloon: 6144`) / 20 GB boot + 32 GB DB disk (`backup=0`) | iGPU `00:02` (HD 530) |
| 171 | `haos` | ❓ (DHCP, no static IP set) | ⏸️ **stopped** | Home Assistant OS — ❓ not in use yet; no service page | 2 / 2 GB / 32 GB | — |

Boot order: TrueNAS starts first (`startup: order=1,up=120`), then Immich (`order=2`), because Immich mounts its library from TrueNAS over NFS.

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
| [Monitoring Pi](../hardware/monitoring-pi.md) | `raspberrypi` | `10.10.0.6` | Glance, Dozzle, Uptime Kuma, Homepage (to be removed) |
| [MSI Raider GE68 HX](../hardware/msi-raider-ge68hx.md) | — (client) | DHCP | Nothing (client) |
| [M1 MacBook Air](../hardware/macbook-air.md) | — (client) | DHCP | Nothing (client) |
