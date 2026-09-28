# Changelog

Newest first. One entry per change to the lab, in the same commit as the doc update.

## 2026-09-28

Doc corrections (no lab changes):

- Immich is **public** at [immich.andrims.net](https://immich.andrims.net) via a **Cloudflare Tunnel** (not NPM). Added a planned move to DNS-only + NPM.
- M1 MacBook Air has **no lab role** — it was never set up as a Jellyfin transcoder. Jellyfin transcodes on the GF65.
- MSI Raider GE68 HX runs **Windows 11 only** (CachyOS dual-boot not done).
- Added LAN IP fields for every machine, and Domain + LAN URL links for every service ([Services → Quick links](services/index.md#quick-links)).
- Recorded hostnames/IPs: Proxmox host `PVE-7050` (Dell OptiPlex 7050 **SFF**) at `10.10.0.15`; `Server-GF65` at `10.10.0.140`. Raider and MacBook are DHCP clients.
- Recorded NPM domains for Vaultwarden, TrueNAS, Paperless, Servarr (`servarr.andrims.net` + `/radarr`, `/sonarr`, `/prowlarr`, `/bazarr`), Glance (`dash.`), NPM (`nginx.`), AdGuard (`dns.`), Proxmox (`pve-7050.`), Dozzle, Uptime Kuma. Added Bazarr and qBittorrent to the Servarr stack.
- Service "Host" now names the specific VM/CT on PVE-7050 instead of just "Proxmox" (IDs still ❓).

## 2026-09-27

- Created `homelab-docs` repo and initial documentation of the existing lab (backfilled from notes; many details still ❓ — see [Open questions](open-questions.md)).
