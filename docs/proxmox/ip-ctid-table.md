# IP / CTID table

All guests run on **`PVE-7050`** (`10.10.0.15`). **One row per guest.** Update this in the same commit as any change to the lab.

!!! note "IP convention"
    Most guests get `10.10.0.<CTID last 3 digits>` (e.g. CT 106 → `.106`). **AdGuard (CT 153 → `.53`) and TrueNAS (VM 104 → `.22`) are exceptions** — don't assume the pattern for those two. Rows marked *(inferred)* follow the pattern but haven't been confirmed yet.

| CTID / VMID | Type | Name | IP | Deployed via | Runs | Resources (cores / RAM / disk) |
|---|---|---|---|---|---|---|
| 101 | LXC | npm | `10.10.0.101` *(inferred)* | ❓ Helper script? | [Nginx Proxy Manager](../network/reverse-proxy.md) | ❓ |
| 102 | LXC | cloudflared | `10.10.0.102` *(inferred)* | Helper script | Cloudflare Tunnel daemon for [Immich](../services/immich.md) | ❓ |
| 104 | VM | TrueNAS | `10.10.0.22` | ❓ | [TrueNAS](../services/truenas.md) | ❓ |
| 105 | LXC | paperless | `10.10.0.105` *(inferred)* | ❓ Helper script? | [Paperless-ngx](../services/paperless-ngx.md) | ❓ |
| 106 | LXC | forgejo | `10.10.0.106` | Helper script | [Forgejo](../services/forgejo.md) | 1 / 512 MB / 4–6 GB |
| 107 | VM | immich | `10.10.0.107` | ❓ Debian VM — helper script or manual Docker compose inside? | [Immich](../services/immich.md) | ❓ |
| 108 | LXC | ❓ | ❓ | ❓ | ❓ Docker — which containers? (not yet identified) | ❓ |
| 109 | LXC | ddns-updater | `10.10.0.109` *(inferred)* | Helper script | Keeps `stream.andrims.net`'s DNS-only record pointed at the home IP — see [Cloudflare](../network/cloudflare.md) | ❓ |
| 110 | LXC | actualbudget | `10.10.0.110` *(inferred)* | Helper script | [ActualBudget](../services/actualbudget.md) — personal finance | ❓ |
| 153 | LXC | adguard | `10.10.0.53` | ❓ | [AdGuard Home](../network/dns-adguard.md) | ❓ |
| 🟡 TBD | LXC | docs | 🟡 | Helper script / manual | [Docs site](../services/docs-site.md) | ❓ |

!!! tip "Quick way to confirm this"
    On the Proxmox host:
    ```bash
    pct list          # LXCs
    qm list           # VMs
    for id in $(pct list | awk 'NR>1{print $1}'); do echo "== $id"; pct config $id | grep -E '^(hostname|net0|cores|memory|rootfs)'; done
    ```
    And inside CT 108, whatever's there: `docker ps --format '{{.Names}}\t{{.Image}}\t{{.Ports}}'`.

## Machines outside Proxmox

| Machine | Hostname | LAN IP | Runs |
|---|---|---|---|
| [Dell OptiPlex 7050 SFF](../hardware/optiplex-7050.md) (Proxmox host) | `PVE-7050` | `10.10.0.15` | Proxmox VE (guests above) |
| [MSI GF65](../hardware/msi-gf65.md) | `Server-GF65` | `10.10.0.140` | Jellyfin, Radarr, Sonarr, Prowlarr, Bazarr, qBittorrent, Vaultwarden |
| [Monitoring Pi](../hardware/monitoring-pi.md) | ❓ | ❓ | Glance |
| [MSI Raider GE68 HX](../hardware/msi-raider-ge68hx.md) | ❓ | DHCP | Nothing (client) |
| [M1 MacBook Air](../hardware/macbook-air.md) | ❓ | DHCP | Nothing (client) |
