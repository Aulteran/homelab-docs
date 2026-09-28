# Roadmap & rules

## Standing rules

1. **Proxmox deploys:** use an **LXC via a community helper script** whenever possible. VMs only when needed.
2. **Monitoring Pi is for monitoring only.**
3. **No Prometheus or Grafana on the Monitoring Pi while it boots from microSD.** Move storage to a USB SSD first (a USB stick at minimum). Consider Beszel as a lighter option.
4. **Internal-only by default.** Only Jellyfin ([stream.andrims.net](https://stream.andrims.net)), Seerr ([request.andrims.net](https://request.andrims.net), tunneled), Immich ([immich.andrims.net](https://immich.andrims.net)) and Vaultwarden ([vault.andrims.net](https://vault.andrims.net), tunneled because the web vault needs HTTPS to work at all) are public. Everything else is AdGuard rewrite + NPM + Tailscale for remote.
5. **No secrets in this repo.** Reference Vaultwarden entries.
6. **Every lab change gets a changelog entry** in the same commit.

## Planned

| Item | Where | Notes |
|---|---|---|
| Move Immich off Cloudflare Tunnel | Cloudflare DNS-only record + NPM | [page](services/immich.md#planned-move-from-cloudflare-tunnel-to-direct-ip-npm) — avoids the 100 MB upload cap; same path as Jellyfin |
| Forgejo remaining setup (Forgejo itself is ✅ running as CT 106) | PVE-7050 | [page](services/forgejo.md) — confirm AdGuard rewrite, NPM proxy host, SSH clone, first push, push mirror |
| GitHub push mirror | Forgejo → private GitHub | |
| Docs site auto-rebuild | CT 105 (`docker`) | [page](services/docs-site.md) — site is ✅ served; still to add the Forgejo push webhook that rebuilds with Zensical (hourly cron as backup) |
| Confirm Paperless-ngx on the docker CT | CT 105 (`docker`) | [page](services/paperless-ngx.md) — the old standalone `paperless` LXC is gone; confirm it runs as a Compose stack on CT 105 |
| Dozzle agent rollout | Confirm per-host | [page](services/dozzle.md) — main instance is ✅ running; agent coverage on GF65 and CT 105 still needs confirming |
| Home Assistant OS | PVE-7050 → VM 171 (exists, stopped) | No service page yet. Check RAM first — starting it adds 2 GB of VM allocation (see [Proxmox host](proxmox/host.md#resource-picture)) |
| Uptime Kuma | Monitoring Pi | [page](services/uptime-kuma.md) |
| Prometheus + Grafana | Monitoring Pi | **Blocked** by rule 3 |
| Forgejo Actions runner | PVE-7050 → new CT | **Optional.** The docs site already rebuilds on push via a webhook; only worth it if other projects need CI |

## Known risks / tech debt

| Risk | Impact | Fix |
|---|---|---|
| MSI GF65 runs Windows 10 (no security updates since Oct 2025) and hosts **publicly-reachable** Vaultwarden | Security; most important data on weakest box, now facing the internet | Back up Vaultwarden off the laptop now; later move GF65 to Debian or Proxmox; consider strong 2FA + admin-panel lockdown given the public exposure |
| Docker Desktop only starts after login | GF65 services stay down after reboot | Auto-login as stopgap, OS migration as fix |
| Monitoring Pi on microSD | Card wear / failure | USB SSD |
| Proxmox RAM ~87%; running guests allocated ~24.6 GB of 24 GB | Limited headroom — no room for another VM as things stand | Watch the TrueNAS (VM 104, 10 GB) and Immich (VM 107, 7 GB) VMs. Trim a VM allocation or add RAM before running HAOS (VM 171) full-time. |
| Immich public via Cloudflare Tunnel | 100 MB per-request upload cap; two different public paths to troubleshoot | Move to DNS-only + NPM (planned) |
| TrueNAS pool has **no redundancy** (single 4 TB disk, SATA-controller-passthrough to VM 104) | A drive failure loses the whole ~900 GB Immich library | Get a working off-box backup in place (see [TrueNAS](services/truenas.md#disks)); a second disk for a mirror would help but doesn't replace backups |
| Backups largely undocumented | Unknown recoverability | Fill in Backups sections, write restore runbooks |
