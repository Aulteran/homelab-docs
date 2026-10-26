# Open questions

Everything marked ❓ in the docs, collected in one place. Tick them off as you verify.

## Hardware
- [x] GF65 specs and where media is stored: documented
- [x] GF65: NVENC transcoding is on; Docker Desktop data is on the boot SSD; no auto-login (manual RDP login after any restart); Curiosity is a Seagate Backup Plus Portable
- [x] Jellyfin starts on boot; Curiosity has no copy (only its family photos/videos are also in Immich)
- [ ] Jellyfin 12: find why the server randomly stops (logs / Event Viewer), and add an Uptime Kuma check for it
- [ ] GF65: does it power back on after a power cut (BIOS can't be reached to set it)? Does Windows show on an external monitor?
- [ ] Back up Curiosity's non-Immich content (old video projects, other files) — **urgent**: its file system has been flagged "Full Repair Needed" since 2026-09-27; copy first, then `chkdsk /F`
- [ ] GF65: what are the 17 unused Docker volumes (1.7 GB)? Delete `C:\Users\admin\jfstat-pre-fix.dump` once Jellystat is confirmed fine
- [x] Monitoring Pi runs Docker: Dozzle, Glance, Uptime Kuma, Homepage (being decommissioned)
- [ ] Decommission Homepage on the Monitoring Pi (Glance replaces it); remove its domain/NPM proxy host and AdGuard rewrite if it has one

## Network
- [x] UniFi subnets/VLANs: one flat `10.10.0.0/24`, no VLANs
- [ ] UniFi: controller location, DHCP reservations, AP model(s), firewall rules (gateway = UX7, switch = USW-Flex-Mini-5-port — both confirmed)
- [ ] AdGuard: secondary DNS / redundancy if AdGuard itself goes down (upstream chain now confirmed: `1.1.1.1` → Quad9 DoH → `10.10.0.1`)
- [ ] NPM: host/CTID, IP, cert method (HTTP vs. Cloudflare DNS challenge), full proxy host list
- [ ] Cloudflare: full DNS record list; `andrims.com` — confirm if/how it's used (registrar + renewal date confirmed for both domains)
- [ ] **NPM Access List on every internal proxy host** (LAN + Tailscale only) — see [Cloudflare → Home IP exposure](network/cloudflare.md#home-ip-exposure)
- [ ] **Decide how to expose Jellyfin without the home IP**: [options and budgets](network/jellyfin-exposure-options.md). Then: provider, region and shape, and whether Immich moves onto it later
- [ ] Cloudflare Tunnel: tunnel name (runs on CT 102, `10.10.0.102` — IP now confirmed)
- [x] Tailscale: app connector routes `*.andrims.net` via Tailscale on PVE-7050 (documented)
- [x] Tailscale: full device list recorded (2026-10-25)
- [ ] Tailscale: what were `oracle1` / `oracle2` and `ltbh-f15`, and can they be removed? Should `Bowswa@`'s devices be limited by an ACL?
- [x] PVE-7050: `enp4s0` / `enp5s0` are stale config from a removed card; both PCIe slots are empty

## Proxmox
- [x] Version (9.2.20), node name, storage pools
- [ ] **Set up backups** — none exist today for the host, VMs or CTs. Pick a schedule and a destination (separate disk or machine)
- [ ] Updates are manual and irregular — decide a cadence (the `pve-no-subscription` repo is already in use)
- [x] CT 102 and CT 153 use **`net1`**, not `net0`
- [x] VM core counts recorded; VM 171 (HAOS) has no static IP set
- [ ] Clear out the old Nov 2025 dumps in `/var/lib/vz/dump` (9.9 GB, includes deleted VM 170)
- [ ] Home Assistant OS (VM 171, stopped): keep, start, or delete? Needs RAM headroom first
- [x] All LXCs (CT 101, 102, 105, 106, 109, 110, 153) were deployed with community helper scripts

## Services
- [ ] Docs site: webhook auto-rebuild still to set up (site itself is live on `:8088`)
- [ ] Are the rewrites individual entries or one `*.andrims.net` wildcard?
- [x] Uptime Kuma is running ([uptime.andrims.net](https://uptime.andrims.net)); Dozzle too
- [ ] **LAN URL for every service** — see [Services → Quick links](services/index.md#quick-links)
- [ ] Vaultwarden: compose, data path, **backup** (URL and exposure now confirmed — see its page)
- [x] Immich: NFS 4.1 mount from TrueNAS, daily DB dumps (on the same TrueNAS share), Docker Compose in `/root/docker/immich-docker-ct/`
- [x] Immich uses the iGPU for QuickSync transcoding; the ML balancer points at GF65 and GE68 workers that aren't listening
- [ ] Immich: keep the remote ML workers (start them on GF65/GE68) or drop the balancer? What's on port 9090? Planned move to an LXC: [runbook](runbooks/immich-vm-to-lxc.md)
- [ ] TrueNAS (VM 104): exact SCALE version, pool/dataset layout, snapshot schedule, **off-box copy (no redundancy — priority)**; set up SSH access
- [x] Paperless-ngx is running on CT 105 (`docker`), port 8001
- [x] Paperless-ngx is a Compose project (`paperless-ault`)
- [ ] Paperless-ngx: data paths, NPM proxy host, export backup
- [ ] Jellyfin: Docker or native, paths, config backup
- [ ] Servarr (`servarr` Compose project, 11 containers): compose path, what routes through Gluetun, ports/domains for Lidarr, Tdarr and Jellystat, backup location
- [x] Jellystat fixed 2026-10-23 (duplicate episode rows broke a migration) — see the [GF65 page](hardware/msi-gf65.md#what-runs-here)
- [ ] Glance: config path, widgets
- [ ] Uptime Kuma: notification channel
- [ ] ActualBudget (CT 110): NPM proxy host for [budget.andrims.net](https://budget.andrims.net), backup
