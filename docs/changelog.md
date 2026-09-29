# Changelog

Newest first. One entry per change to the lab, in the same commit as the doc update.

## 2026-09-29 (13)

- **Server-GF65 can't run Linux.** Its screen and speakers have been dead since 2022 (suspected motherboard short while redoing the thermal paste), and the BIOS doesn't show on an external monitor, so Secure Boot can't be disabled. Windows is permanent, and the box is managed over RDP only. Documented at the top of the GF65 page.
- **Replacement server planned** (better Jellyfin performance, good drives in RAID), waiting on budget. Added to the roadmap.
- **GF65 containers listed** from Dozzle: the `servarr` Compose project has 11 containers (bazarr, gluetun, jellystat-db, lidarr, prowlarr, qbittorrent, radarr, seerr, sonarr, tdarr, jellystat), plus `vaultwarden` and `dozzle-agent`. The Dozzle agent on the GF65 is confirmed running. **Jellystat has been down for a few days**, not yet diagnosed.
- **Jellyfin isn't a Windows service** (it doesn't appear in Services). It runs as the tray app.

## 2026-09-29 (12)

- **Known issue documented:** since updating to Jellyfin 12, the Jellyfin server service randomly turns off. It's restarted by hand: RDP into Server-GF65 and start it from the Jellyfin tray app. Cause not found. Jellyfin does start on boot.
- **Curiosity has no copy anywhere.** Only its family photos and videos are also in Immich (TrueNAS SATA SSD, no redundancy). Everything else on it is single-copy. Updated the GF65 page, roadmap and open questions.

## 2026-09-29 (11)

- **GF65 details confirmed:** Jellyfin uses NVENC hardware transcoding on the 1660 Ti. Docker Desktop's data is on the nearly full boot SSD. There's no auto-login, so after any restart the Docker services stay down until a manual RDP login. Curiosity is a Seagate Backup Plus Portable.

## 2026-09-29 (10)

- **Server-GF65 specs recorded:** i7-9750H, 16 GB DDR4, GTX 1660 Ti laptop GPU, 256 GB boot SSD (nearly always full). Two USB hard disks: **Neo** (2 TB Seagate Backup Plus Ultra Touch, all Jellyfin media) and **Curiosity** (5 TB Seagate, long-term storage of old files, family photos and video projects).
- **Jellyfin is a native Windows install** on the boot SSD, not a Docker container. Updated the Jellyfin and GF65 pages.
- **New risks:** full boot SSD, and irreplaceable data on Curiosity with no known backup.

## 2026-09-29 (9)

- **Deployment method confirmed:** CT 101, 102, 105, 106, 109, 110 and 153 were all created with community helper scripts. Updated the IP / CTID table.

## 2026-09-29 (8)

- **Tailscale IPs recorded:** PVE-7050 `100.110.0.15`, Server-GF65 `100.69.160.6`, Monitoring Pi `100.101.228.37`. Added to the Tailscale page and each hardware page. Server-GF65 is always on.
- **Home page and hardware index cleaned up:** Paperless-ngx and Uptime Kuma now ✅ (table, at-a-glance and network map), ActualBudget shows its domain, and the Pi lists Uptime Kuma.

## 2026-09-29 (7)

- **Monitoring Pi contents confirmed** (from Dozzle): four Docker containers — `dozzle`, `glance`, `homepage` and `uptime-kuma-uptime-kuma-1`. The Pi does use Docker. Uptime Kuma marked running there.
- **Homepage is to be decommissioned**, since Glance replaces it. Added to the roadmap and open questions.

## 2026-09-29 (6)

- **Tailscale:** login is GitHub via the `AndrimsDevs` organization. Server-GF65 is an exit node (rarely used). PVE-7050's own resolver points at AdGuard (`10.10.0.53`), which is what the app connector relies on.

## 2026-09-29 (5)

- **Tailscale details recorded:** tailnet DNS name `chocolate-wall.ts.net`; MagicDNS on with default DNS only; **no ACLs** (default allow-all). The app connector and Tailscale run directly on the PVE-7050 host, not in a guest. PVE-7050 advertises `10.10.0.0/24` but the route stays disabled in the admin console until needed. Tailscale runs with `--accept-dns=false` because accepting tailnet DNS broke DNS for the LXCs. Added the gotchas to the Tailscale page and a Tailscale row to the Proxmox host page.

## 2026-09-29 (4)

- **Tailscale remote access documented:** an app connector sends every `*.andrims.net` request from a tailnet device to Tailscale on PVE-7050, which drops it onto the LAN: AdGuard for DNS, then NPM. Works like being at home. Noted PVE-7050 as a single point of failure for remote access. Updated the Tailscale and network pages.

## 2026-09-29 (3)

- **Upstream gateway documented:** the Xfinity gateway is at `10.0.0.1`, in bridge mode, passing the public IP to the UX7. Its admin panel is reachable from anywhere on the LAN (used to disable bridge mode). Added to the UniFi and network pages.

## 2026-09-29 (2)

- **Network:** one flat subnet, `10.10.0.0/24` (gateway `10.10.0.1`), **no VLANs**. Recorded on the network and UniFi pages. A separate homelab VLAN is planned, not started (added to the roadmap).

## 2026-09-29 (1)

- **Paperless-ngx confirmed running** on CT 105 (`docker`) at [http://10.10.0.105:8001](http://10.10.0.105:8001) (port 8001, not the default 8000).
- **Uptime Kuma confirmed running** at [uptime.andrims.net](https://uptime.andrims.net); dropped "planned".
- **ActualBudget domain:** [budget.andrims.net](https://budget.andrims.net).
- **Proxmox host:** version is **9.2.20**. **No backups exist** for the host, VMs or CTs (added as a known risk). Updates are manual and irregular (also added as a risk).
- **Services quick links:** the Host column now shows just the VM or CT ID for Proxmox guests, without `PVE-7050 →`.

## 2026-09-28 (14)

- **New page: [Jellyfin exposure options](network/jellyfin-exposure-options.md).** Six ways to make Jellyfin reachable without publishing the home IP: edge VPS + NPM (current plan), VPS + Pangolin/WireGuard, Cloudflare Tunnel (terms grey zone), Tailscale Funnel (bandwidth limits, `ts.net` names only), Pangolin Cloud, and private-only Tailscale. Includes a VPS provider and budget table (Oracle Always Free $0, BuyVM NY slice $3.50/mo, Hetzner from €5.49/mo after the June 2026 price rises) and traffic estimates. No decision recorded yet.
- **Corrections to the [Edge VPS](network/edge-vps.md) plan:** Oracle's free AMD micro shape is capped at 50 Mbps (use the Ampere A1 shape), Oracle reclaims instances that stay under 20% CPU/network for 7 days, and the VPS can see the home IP over a direct WireGuard link.

## 2026-09-28 (13)

- **Plan recorded: edge VPS.** A cloud VM (Oracle Free Tier considered) runs NPM, joins the tailnet, and becomes the only public entry point: public DNS points at the VM, which forwards over Tailscale to the lab. Goal: the home IP is published nowhere and ports 80/443 at home are closed. New page [Edge VPS](network/edge-vps.md), covering the design, trade-offs (VM hardening, Tailscale ACL, real client IPs, DERP relay speed) and a rollout order.
- Immich's move off the tunnel is now planned **through the VPS**, which resolves the conflict with the home-IP rule. Added to the roadmap, network overview, Cloudflare, Tailscale and Immich pages.

## 2026-09-28 (12)

- **Seerr confirmed:** Docker Desktop on Server-GF65, port `5055`, same Cloudflare Tunnel (CT 102) as Immich and Vaultwarden. One tunnel serves all three.
- **Rule 4 extended:** the home IP is published only under `stream.andrims.net`. Other public services go through the tunnel.
- **Public DNS checked:** `stream.andrims.net` is a CNAME → `aultmain.andrims.net`, which answers with the home IP (resolves the old "stream vs aultmain" question). `immich.`, `vault.`, `request.` and the root domain answer with Cloudflare addresses. Internal names have no public records, and there's no wildcard. New section: [Cloudflare → Home IP exposure](network/cloudflare.md#home-ip-exposure).
- **New known risk:** NPM receives ports 80/443 and answers for internal proxy hosts too, so they can be reached from the internet by hostname. Fix is an NPM Access List on each one.
- **Flagged:** the planned Immich move to a direct-IP record conflicts with the new rule. Left in place, marked for a decision.

## 2026-09-28 (11)

- **Seerr details:** it runs on **Server-GF65** and is public at [request.andrims.net](https://request.andrims.net) through the **Cloudflare Tunnel** (not NPM), like Vaultwarden. Added it to the Cloudflare DNS records and tunnel hostnames, the network map and traffic flows, the GF65 page, and the Services, Servarr and NPM tables. LAN link uses the default port `5055`.
- The "retire the tunnel" plan is reworded: Immich still moves off it, but the tunnel and `cloudflared` (CT 102) stay for Vaultwarden and Seerr.

## 2026-09-28 (10)

- **Public vs internal settled:** only **Jellyfin, Seerr, Vaultwarden and Immich** are public. Every other service's "Public?" ❓ is now **No** (Services quick links and NPM proxy hosts).
- **Added Seerr** (formerly Jellyseerr, media requests), which is public. Its host, domain and exposure path are still ❓. Updated the home page, roadmap rule 4 and the Servarr apps table.

## 2026-09-28 (9)

- **Docs site is live** at [docs.andrims.net](https://docs.andrims.net) and [http://10.10.0.105:8088](http://10.10.0.105:8088) (nginx container on CT 105). Port `8088` confirmed; AdGuard rewrite and NPM proxy host marked ✅. The webhook auto-rebuild is the only piece left.

## 2026-09-28 (8)

Proxmox inventory checked against `pct list`, `qm list` and `pct config` on PVE-7050:

- **CT 100, 103 and 108 are deleted.** The old standalone `paperless` LXC is gone too: **CT 105 is now `docker`**, the shared Docker host (2 cores / 2 GB / 32 GB, `10.10.0.105`). The planned "new docker CT" is this one, so there's no separate TBD row any more.
- **Docs site** now runs on CT 105: every `<docker-ct-ip>` placeholder is now `10.10.0.105` (docs-site, Forgejo webhook/allow-list, NPM). The site is served; the webhook auto-rebuild is still to do. The nginx port (`8088` vs `8080`) is marked ❓.
- **Paperless-ngx** is marked ❓ until it's confirmed running as a container on CT 105.
- **New:** VM 171 `haos` (Home Assistant OS), **stopped**, 2 GB RAM / 32 GB disk. Added to the table and roadmap, with a RAM warning: running guests are already allocated ~24.6 GB of the 24 GB installed.
- **Confirmed:** CT 109 IP (`10.10.0.109`), and real hostnames — `nginxproxymanager` (101), `adguard-alpine` (153), `debian-immich` (VM 107). Resources (cores / RAM / disk) filled in for every guest. No more *(inferred)* IPs.
- **Oddity:** CT 102 (cloudflared) and CT 153 (AdGuard) show no `net0` line in `pct config`. Their IPs are kept from earlier confirmation and flagged ⚠️.

## 2026-09-28 (7)

Docs-site plan settled (docs only, no lab changes yet):

- [Docs site](services/docs-site.md) rewritten as a full deployment page. Zensical is only the builder, not a service. The site is served by an `nginx:alpine` container on the planned **"docker" CT** (`:8088`), not its own CT, and not by NPM directly.
- **Auto-rebuild on push:** Forgejo push webhook → `webhook` listener on the docker CT (`:9000`, HMAC-signed, `main` only) → `build-docs.sh` (`git pull` → `zensical build` → `rsync`). An hourly cron job is the backup trigger. Repo access is a read-only deploy key; the webhook secret lives in Vaultwarden.
- [Forgejo](services/forgejo.md): new Webhooks section. `ALLOWED_HOST_LIST` has to include the docker CT, because Forgejo blocks webhooks to private IPs by default.
- Removed the separate "docs" CT row from the [IP / CTID table](proxmox/ip-ctid-table.md); updated NPM, Services, home page map and Roadmap to match. Forgejo Actions is now marked optional.

## 2026-09-28 (6)

More corrections and confirmations from Aadil:

- **Monitoring Pi:** hostname is `raspberrypi` (the default, never changed); it's a **Raspberry Pi 5 Model B**, 4 GB RAM, running Debian 13 (trixie) aarch64 — confirmed via a fastfetch screenshot. GE68 and MacBook Air hostnames were never going to matter (both are DHCP clients), so those fields now just say so instead of sitting as ❓.
- **UniFi hardware named:** gateway is a **UniFi UX7**, with a **USW-Flex-Mini-5-port** switch hooked up to it. Reflected in the network map and [UniFi](network/unifi.md) (dropped the "Location" column there for consistency — everything's at home, already established).
- **Port forwards confirmed:** both **80 and 443** forward straight from the UX7 to NPM (`10.10.0.101`, CT 101) — resolves the last ❓ on the Jellyfin traffic-flow path.
- **AdGuard upstream DNS confirmed:** `1.1.1.1` primary → Quad9 DoH (`dns10.quad9.net`) backup → `10.10.0.1` (likely the ISP/Xfinity router) as a third fallback.
- **Cloudflare:** it's the registrar (not just DNS) for `andrims.net`, renewing Nov 22, 2028. Also holds `andrims.com`, renewing Nov 8, 2028 — purpose still ❓.
- **DDNS updater (CT 109) — correction, not just a confirmation:** the docs previously assumed it kept `stream.andrims.net` current. Pulling the actual config shows it only has one live entry, for **`aultmain.andrims.net`** via the Cloudflare provider (plus an unused leftover `namecheap`/`example.com` template entry). Whether `stream.andrims.net` is separately maintained or CNAME'd onto `aultmain.andrims.net` is now an open question. Added a standing reminder that the provider config lives at `/opt/ddns-updater/data/config.json` on the CT and needs editing there before switching DNS targets — **without** copying the file's actual API token/zone ID into this repo (it's live credentials; [rule 5](roadmap.md#standing-rules) says no secrets here).
- **CT 102 (cloudflared) IP confirmed:** `10.10.0.102`, no longer *(inferred)*.

## 2026-09-28 (5)

LAN URLs confirmed by Aadil — Forgejo, ActualBudget (note: **HTTPS**, port 5006), Glance, NPM, AdGuard, TrueNAS and Immich all now have real, working LAN links instead of placeholders. Corrected qBittorrent's port from an assumed 8080 to the real **8081**.

**Vaultwarden is actually public**, exposed via Cloudflare Tunnel (`vault.andrims.net`) alongside Immich — a fact worth explaining, not just recording: its web vault uses WebCrypto, which refuses to run outside a secure context, so the plain LAN URL (`http://10.10.0.140:8000`) loads but doesn't really work. The tunnel is the workaround. Updated everywhere this matters: the home page (now three public services, not two), the NPM/AdGuard tables (removed — it was never actually an NPM host), Cloudflare's public-DNS and tunnel sections, the network map, and the roadmap's standing rules and known risks. Also noted on its page, as a documentation option rather than a plan: an internal NPM certificate would give it working HTTPS without the public exposure, the same way Paperless-ngx works.

## 2026-09-28 (4)

Hardware corrections from Aadil:

- **PVE-7050:** 24 GB RAM (8 GB × 3), single onboard 1 GbE NIC (no extras), 512 GB boot SSD split into LVM + LVM-thin. **Corrected: no ZFS at the Proxmox host level** — the earlier "ZFS ARC" explanation for RAM usage was wrong. Removed the Location field (everything's at home).
- **Storage:** a separate 4 TB WD SATA SSD is plugged into the motherboard's SATA ports; the whole SATA controller is passed through to **TrueNAS (VM 104)**, which manages the disk itself. This resolves the old "how are the disks given to the VM" question — and surfaces a real risk: it's a **single disk, no redundancy**, added to the roadmap's known risks.
- **RAM allocations confirmed:** TrueNAS (VM 104) = 10 GB, Immich (VM 107) = 7 GB. Together with the LXCs, this fully explains the ~87% RAM usage without needing host-level ZFS ARC.

## 2026-09-28 (3)

More corrections from Aadil:

- **Monitoring Pi** LAN IP is `10.10.0.6`.
- **Dozzle** and **Forgejo** are confirmed **✅ running** (both were marked planned/in-progress before). Updated status everywhere (home page, Services quick links, hardware pages, NPM/AdGuard tables, roadmap).
- **CT 103 and CT 108 are retired**, no longer in service — the earlier "CT 108 unidentified, might be a Docker host" question is now moot; not worth digging into either one further.
- **Planned:** consolidate lightweight Docker Compose services (Paperless-ngx, and the future docs-site nginx container) onto **one new "docker" CT**, instead of giving each its own. Once that's live, **CT 100, 103, 105 and 108 all get deleted**. Reflected in the [IP / CTID table](proxmox/ip-ctid-table.md), [Roadmap](roadmap.md#planned), and the network map.

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
