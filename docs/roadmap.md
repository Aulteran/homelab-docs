# Roadmap & rules

## Standing rules

1. **Proxmox deploys:** use an **LXC via a community helper script** whenever possible. VMs only when needed.
2. **Monitoring Pi is for monitoring only.**
3. **No Prometheus or Grafana on the Monitoring Pi while it boots from microSD.** Move storage to a USB SSD first (a USB stick at minimum). Consider Beszel as a lighter option.
4. **Internal-only by default.** Only Jellyfin ([stream.andrims.net](https://stream.andrims.net)) and Immich ([immich.andrims.net](https://immich.andrims.net)) are public. Everything else is AdGuard rewrite + NPM + Tailscale for remote.
5. **No secrets in this repo.** Reference Vaultwarden entries.
6. **Every lab change gets a changelog entry** in the same commit.

## Planned

| Item | Where | Notes |
|---|---|---|
| Move Immich off Cloudflare Tunnel | Cloudflare DNS-only record + NPM | [page](services/immich.md#planned-move-from-cloudflare-tunnel-to-direct-ip-npm) — avoids the 100 MB upload cap; same path as Jellyfin |
| Forgejo setup (LXC already deployed as CT 106) | PVE-7050 | [page](services/forgejo.md) — AdGuard rewrite, NPM proxy host, SSH clone, first push |
| GitHub push mirror | Forgejo → private GitHub | |
| Docs site | PVE-7050 → new CT | [page](services/docs-site.md) |
| Dozzle + agents | Monitoring Pi + all Docker hosts | [page](services/dozzle.md) |
| Uptime Kuma | Monitoring Pi | [page](services/uptime-kuma.md) |
| Prometheus + Grafana | Monitoring Pi | **Blocked** by rule 3 |
| Forgejo Actions runner | PVE-7050 → new CT | Replaces docs cron build |

## Known risks / tech debt

| Risk | Impact | Fix |
|---|---|---|
| MSI GF65 runs Windows 10 (no security updates since Oct 2025) and hosts Vaultwarden | Security; most important data on weakest box | Back up Vaultwarden off the laptop now; later move GF65 to Debian or Proxmox |
| Docker Desktop only starts after login | GF65 services stay down after reboot | Auto-login as stopgap, OS migration as fix |
| Monitoring Pi on microSD | Card wear / failure | USB SSD |
| Proxmox RAM ~87% | Limited headroom | Watch the TrueNAS (VM 104) and Immich (VM 107) VMs, and CT 108 once it's identified; ZFS ARC can be capped if needed |
| Immich public via Cloudflare Tunnel | 100 MB per-request upload cap; two different public paths to troubleshoot | Move to DNS-only + NPM (planned) |
| Backups largely undocumented | Unknown recoverability | Fill in Backups sections, write restore runbooks |
