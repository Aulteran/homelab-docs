# Changelog

Newest first. One entry per change to the lab, in the same commit as the doc update.

## 2026-09-28 (2)

Confirmed CT/VM assignments on PVE-7050 (from Aadil directly):

- AdGuard = CT 153, `10.10.0.53`; TrueNAS = VM 104, `10.10.0.22` — **both break the usual IP-from-CTID pattern**, called out in the [IP / CTID table](proxmox/ip-ctid-table.md).
- Immich = VM 107 (Debian), `10.10.0.107`; Forgejo = CT 106, `10.10.0.106` — both match the pattern (CTID's last 3 digits = IP's last octet).
- Paperless-ngx = CT 105 — this **replaces** the old guess that CT 105 was a generic Docker-in-LXC host. Only **CT 108** is still unidentified.
- New: NPM (CT 101), Cloudflare Tunnel daemon `cloudflared` (CT 102, resolves where the Immich tunnel runs), a DDNS updater keeping `stream.andrims.net` current (CT 109), and **ActualBudget** (CT 110, personal finance — new service page added).
- IPs for CT 101, 102, 105, 109, 110 are inferred from the CTID pattern, not yet confirmed — marked *(inferred)* throughout.
- Forgejo status moved from "planned" to "in progress" — the LXC exists; the rest of its setup checklist (AdGuard rewrite, NPM host, SSH clone, first push) is still open.

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
