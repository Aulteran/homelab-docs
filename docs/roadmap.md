# Roadmap & rules

## Standing rules

1. **Proxmox deploys:** use an **LXC via a community helper script** whenever possible. VMs only when needed.
2. **Monitoring Pi is for monitoring only.**
3. **No Prometheus or Grafana on the Monitoring Pi while it boots from microSD.** Move storage to a USB SSD first (a USB stick at minimum). Consider Beszel as a lighter option.
4. **Internal-only by default.** Only Jellyfin ([stream.andrims.net](https://stream.andrims.net)), Seerr ([request.andrims.net](https://request.andrims.net), tunneled), Immich ([immich.andrims.net](https://immich.andrims.net)) and Vaultwarden ([vault.andrims.net](https://vault.andrims.net), tunneled because the web vault needs HTTPS to work at all) are public. Everything else is AdGuard rewrite + NPM + Tailscale for remote. **The home IP is published only under `stream.andrims.net`**; any other public service goes through the Cloudflare Tunnel. See [Cloudflare → Home IP exposure](network/cloudflare.md#home-ip-exposure).
5. **No secrets in this repo.** Reference Vaultwarden entries.
6. **Every lab change gets a changelog entry** in the same commit.

## Planned

| Item | Where | Notes |
|---|---|---|
| Decommission Homepage | Monitoring Pi | Glance replaces it. Stop and remove the `homepage` container, and clean up any NPM proxy host or AdGuard rewrite it had. |
| Homelab VLAN | UniFi UX7 + Proxmox | Not started. Today everything is on one flat `10.10.0.0/24` ([network](network/index.md#subnets-vlans)). Needs a plan for re-IPing guests (the `10.10.0.<CTID>` convention), AdGuard/NPM reachability from other VLANs, and Tailscale routes. |
| **Edge VPS to hide the home IP** | Cloud VM + NPM, joined to the tailnet | [page](network/edge-vps.md) — public DNS points at the VM, which forwards to the lab over Tailscale. Alternatives and budgets: [options](network/jellyfin-exposure-options.md). Then close ports 80/443 on the UX7 and retire `aultmain`/CT 109. Also lets Immich leave the tunnel without exposing the home IP. |
| Move Immich off Cloudflare Tunnel | Cloudflare DNS-only record + NPM | [page](services/immich.md#planned-move-from-cloudflare-tunnel-to-direct-ip-npm) — avoids the 100 MB upload cap. ⚠️ **Conflicts with rule 4** if done with a home-IP record. Do it **after the [edge VPS](network/edge-vps.md)**, so Immich goes through the VPS instead. |
| Forgejo remaining setup (Forgejo itself is ✅ running as CT 106) | PVE-7050 | [page](services/forgejo.md) — confirm AdGuard rewrite, NPM proxy host, SSH clone, first push, push mirror |
| GitHub push mirror | Forgejo → private GitHub | |
| Docs site auto-rebuild | CT 105 (`docker`) | [page](services/docs-site.md) — site is ✅ served; still to add the Forgejo push webhook that rebuilds with Zensical (hourly cron as backup) |
| Dozzle agent rollout | Confirm per-host | [page](services/dozzle.md) — main instance is ✅ running; agent coverage on GF65 and CT 105 still needs confirming |
| Home Assistant OS | PVE-7050 → VM 171 (exists, stopped) | No service page yet. Check RAM first — starting it adds 2 GB of VM allocation (see [Proxmox host](proxmox/host.md#resource-picture)) |
| Uptime Kuma checks | Monitoring Pi | [page](services/uptime-kuma.md) — running; still to confirm the checks cover every service and pick a notification channel |
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
| NPM serves internal proxy hosts to anyone who reaches the home IP on 443 | Proxmox, TrueNAS, NPM/AdGuard admin etc. reachable from the internet by hostname, despite having no public DNS | NPM Access List (LAN + Tailscale only) on every internal proxy host — see [Cloudflare → Home IP exposure](network/cloudflare.md#home-ip-exposure). The planned [edge VPS](network/edge-vps.md) removes the problem by closing 80/443 at home. |
| **No backups of Proxmox, VMs or CTs** | Losing the 512 GB boot SSD loses every guest on it (NPM, AdGuard, Forgejo, ActualBudget, Paperless, the Immich VM, TrueNAS's boot disk) | vzdump schedule to a separate disk or machine (or Proxmox Backup Server); test a restore |
| Proxmox is updated by hand at irregular intervals | Security patches lag; no known patch level | Pick a cadence (e.g. monthly), take a backup first, write the [update runbook](runbooks/index.md) |
| Backups largely undocumented | Unknown recoverability | Fill in Backups sections, write restore runbooks |
