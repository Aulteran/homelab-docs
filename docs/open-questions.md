# Open questions

Everything marked ❓ in the docs, collected in one place. Tick them off as you verify.

## Hardware
- [ ] GF65: specs, where media is stored, auto-login set?, Jellyfin transcoding method (GPU or software)
- [ ] Monitoring Pi: Docker used for anything?

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
- [ ] Tailscale: device list with Tailscale IPs. (App connector, resolver, login provider, exit node, ACLs, MagicDNS and the disabled subnet route are all documented.)

## Proxmox
- [x] Version (9.2.20), node name, storage pools
- [ ] **Set up backups** — none exist today for the host, VMs or CTs. Pick a schedule and a destination (separate disk or machine)
- [ ] Updates are manual and irregular — decide a cadence and whether to use the no-subscription repo
- [ ] CT 102 and CT 153 show **no `net0`** in `pct config` — check which interface they use (`pct config 102 | grep ^net`)
- [ ] VM core counts and IP for VM 171 (HAOS): `qm config <vmid>`
- [ ] Home Assistant OS (VM 171, stopped): keep, start, or delete? Needs RAM headroom first
- [ ] CT 105 (`docker`): deployed via helper script or manually?

## Services
- [ ] Docs site: webhook auto-rebuild still to set up (site itself is live on `:8088`)
- [ ] Are the rewrites individual entries or one `*.andrims.net` wildcard?
- [x] Uptime Kuma is running ([uptime.andrims.net](https://uptime.andrims.net)); Dozzle too
- [ ] **LAN URL for every service** — see [Services → Quick links](services/index.md#quick-links)
- [ ] Vaultwarden: compose, data path, **backup** (URL and exposure now confirmed — see its page)
- [ ] Immich: how TrueNAS is mounted, DB backup, deployment method inside VM 107
- [ ] TrueNAS (VM 104): SCALE/CORE version, pool/dataset layout on the single 4 TB disk, snapshot schedule, **off-box copy (no redundancy — priority)**
- [x] Paperless-ngx is running on CT 105 (`docker`), port 8001
- [ ] Paperless-ngx: Compose or plain Docker?, data paths, NPM proxy host, export backup
- [ ] Jellyfin: Docker or native, paths, config backup
- [ ] Servarr: compose, download client, other apps, backup location
- [ ] Glance: config path, widgets
- [ ] Uptime Kuma: notification channel
- [ ] ActualBudget (CT 110): NPM proxy host for [budget.andrims.net](https://budget.andrims.net), backup
