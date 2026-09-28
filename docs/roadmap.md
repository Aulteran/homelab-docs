# Roadmap & rules

## Standing rules

1. **Proxmox deploys:** use an **LXC via a community helper script** whenever possible. VMs only when needed.
2. **Monitoring Pi is for monitoring only.**
3. **No Prometheus or Grafana on the Monitoring Pi while it boots from microSD.** Move storage to a USB SSD first (a USB stick at minimum). Consider Beszel as a lighter option.
4. **Internal-only by default.** Only Jellyfin ([stream.andrims.net](https://stream.andrims.net)), Immich ([immich.andrims.net](https://immich.andrims.net)) and Vaultwarden ([vault.andrims.net](https://vault.andrims.net), tunneled because the web vault needs HTTPS to work at all) are public. Everything else is AdGuard rewrite + NPM + Tailscale for remote.
5. **No secrets in this repo.** Reference Vaultwarden entries.
6. **Every lab change gets a changelog entry** in the same commit.

## Planned

| Item | Where | Notes |
|---|---|---|
| Move Immich off Cloudflare Tunnel | Cloudflare DNS-only record + NPM | [page](services/immich.md#planned-move-from-cloudflare-tunnel-to-direct-ip-npm) — avoids the 100 MB upload cap; same path as Jellyfin |
| Forgejo remaining setup (Forgejo itself is ✅ running as CT 106) | PVE-7050 | [page](services/forgejo.md) — confirm AdGuard rewrite, NPM proxy host, SSH clone, first push, push mirror |
| GitHub push mirror | Forgejo → private GitHub | |
| **Consolidate lightweight Docker services onto one new "docker" CT** | PVE-7050 → new CT | Hosts [Paperless-ngx](services/paperless-ngx.md) (migrating off CT 105) and the future docs-site nginx container, instead of each getting its own CT. **After migration, delete CT 100, CT 103, CT 105 and CT 108** (100 and 105 are one-off LXCs being folded in; 103 and 108 are already retired and just need cleanup). |
| Docs site (nginx) | → new "docker" CT above, once it exists | [page](services/docs-site.md) |
| Dozzle agent rollout | Confirm per-host | [page](services/dozzle.md) — main instance is ✅ running; agent coverage on GF65 and the future "docker" CT still needs confirming |
| Uptime Kuma | Monitoring Pi | [page](services/uptime-kuma.md) |
| Prometheus + Grafana | Monitoring Pi | **Blocked** by rule 3 |
| Forgejo Actions runner | PVE-7050 → new CT | Replaces docs cron build |

## Known risks / tech debt

| Risk | Impact | Fix |
|---|---|---|
| MSI GF65 runs Windows 10 (no security updates since Oct 2025) and hosts **publicly-reachable** Vaultwarden | Security; most important data on weakest box, now facing the internet | Back up Vaultwarden off the laptop now; later move GF65 to Debian or Proxmox; consider strong 2FA + admin-panel lockdown given the public exposure |
| Docker Desktop only starts after login | GF65 services stay down after reboot | Auto-login as stopgap, OS migration as fix |
| Monitoring Pi on microSD | Card wear / failure | USB SSD |
| Proxmox RAM ~87% | Limited headroom | Watch the TrueNAS (VM 104) and Immich (VM 107) VMs; ZFS ARC can be capped if needed. Retiring CT 100, 103, 105, 108 (see planned consolidation above) should free some up. |
| Immich public via Cloudflare Tunnel | 100 MB per-request upload cap; two different public paths to troubleshoot | Move to DNS-only + NPM (planned) |
| TrueNAS pool has **no redundancy** (single 4 TB disk, SATA-controller-passthrough to VM 104) | A drive failure loses the whole ~900 GB Immich library | Get a working off-box backup in place (see [TrueNAS](services/truenas.md#disks)); a second disk for a mirror would help but doesn't replace backups |
| Backups largely undocumented | Unknown recoverability | Fill in Backups sections, write restore runbooks |
