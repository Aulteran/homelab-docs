# Roadmap & rules

## Standing rules

1. **Proxmox deploys:** use an **LXC via a community helper script** whenever possible. VMs only when needed.
2. **Monitoring Pi is for monitoring only.**
3. **No Prometheus or Grafana on the Monitoring Pi while it boots from microSD.** Move storage to a USB SSD first (a USB stick at minimum). Consider Beszel as a lighter option.
4. **Internal-only by default.** Only Jellyfin (`stream.andrims.net`) is public. Everything else is AdGuard rewrite + NPM + Tailscale for remote.
5. **No secrets in this repo.** Reference Vaultwarden entries.
6. **Every lab change gets a changelog entry** in the same commit.

## Planned

| Item | Where | Notes |
|---|---|---|
| Forgejo | Proxmox LXC | [page](services/forgejo.md) — first, since these docs live there |
| GitHub push mirror | Forgejo → private GitHub | |
| Docs site | Proxmox LXC | [page](services/docs-site.md) |
| Dozzle + agents | Monitoring Pi + all Docker hosts | [page](services/dozzle.md) |
| Uptime Kuma | Monitoring Pi | [page](services/uptime-kuma.md) |
| Prometheus + Grafana | Monitoring Pi | **Blocked** by rule 3 |
| Forgejo Actions runner | Proxmox LXC | Replaces docs cron build |

## Known risks / tech debt

| Risk | Impact | Fix |
|---|---|---|
| MSI GF65 runs Windows 10 (no security updates since Oct 2025) and hosts Vaultwarden | Security; most important data on weakest box | Back up Vaultwarden off the laptop now; later move GF65 to Debian or Proxmox |
| Docker Desktop only starts after login | GF65 services stay down after reboot | Auto-login as stopgap, OS migration as fix |
| Monitoring Pi on microSD | Card wear / failure | USB SSD |
| Proxmox RAM ~87% | Limited headroom | Watch TrueNAS/Immich VMs and CT 105/108; ZFS ARC can be capped if needed |
| Backups largely undocumented | Unknown recoverability | Fill in Backups sections, write restore runbooks |
