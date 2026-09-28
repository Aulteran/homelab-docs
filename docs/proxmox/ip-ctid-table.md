# IP / CTID table

**One row per guest.** Update this in the same commit as any change to the lab.

| CTID / VMID | Type | Name | IP | Deployed via | Runs | Resources (cores / RAM / disk) |
|---|---|---|---|---|---|---|
| 105 | LXC | ❓ | ❓ | ❓ | Docker — ❓ which containers | ❓ |
| 108 | LXC | ❓ | ❓ | ❓ | Docker — ❓ which containers | ❓ |
| ❓ | VM | TrueNAS | ❓ | ❓ | [TrueNAS](../services/truenas.md) | ❓ |
| ❓ | VM | Immich | ❓ | ❓ | [Immich](../services/immich.md) | ❓ |
| ❓ | ❓ | ❓ | ❓ | ❓ | [AdGuard Home](../network/dns-adguard.md) ❓ | ❓ |
| ❓ | ❓ | ❓ | ❓ | ❓ | [Nginx Proxy Manager](../network/reverse-proxy.md) ❓ | ❓ |
| ❓ | ❓ | ❓ | ❓ | ❓ | [Paperless-ngx](../services/paperless-ngx.md) ❓ | ❓ |
| 🟡 TBD | LXC | forgejo | 🟡 | Helper script | [Forgejo](../services/forgejo.md) | 1 / 512 MB / 4–6 GB (planned) |
| 🟡 TBD | LXC | docs | 🟡 | Helper script / manual | [Docs site](../services/docs-site.md) | ❓ |

!!! tip "Quick way to fill this in"
    On the Proxmox host:
    ```bash
    pct list          # LXCs
    qm list           # VMs
    for id in $(pct list | awk 'NR>1{print $1}'); do echo "== $id"; pct config $id | grep -E '^(hostname|net0|cores|memory|rootfs)'; done
    ```
    And inside each Docker host: `docker ps --format '{{.Names}}\t{{.Image}}\t{{.Ports}}'`.

## Machines outside Proxmox

| Machine | IP | Runs |
|---|---|---|
| [MSI GF65](../hardware/msi-gf65.md) | ❓ | Jellyfin, Radarr, Sonarr, Prowlarr, Vaultwarden |
| [Monitoring Pi](../hardware/monitoring-pi.md) | ❓ | Glance |
| [M1 MacBook Air](../hardware/macbook-air.md) | ❓ | Jellyfin transcoding |
