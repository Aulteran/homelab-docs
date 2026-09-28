# Open questions

Everything marked ❓ in the docs, collected in one place. Tick them off as you verify.

## Hardware
- [ ] Monitoring Pi LAN IP and hostname; Raider and MacBook hostnames
- [ ] OptiPlex: RAM total, disks, ZFS pool layout, NICs
- [ ] GF65: specs, where media is stored, auto-login set?, Jellyfin transcoding method (GPU or software)
- [ ] Monitoring Pi: model, OS, Docker?

## Network
- [ ] UniFi models, controller location, subnets/VLANs, DHCP reservations
- [ ] Port forwards (443/80 → NPM?)
- [ ] AdGuard: host/CTID, IP, upstreams, secondary DNS
- [ ] NPM: host/CTID, IP, cert method (HTTP vs. Cloudflare DNS challenge), full proxy host list
- [ ] Cloudflare: registrar, renewal date, DDNS method, full record list
- [ ] Cloudflare Tunnel: tunnel name, where `cloudflared` runs
- [ ] Tailscale: MagicDNS, DNS settings, subnet router/exit node, device list

## Proxmox
- [ ] Version, node name, storage pools
- [ ] Backup schedule and destination
- [ ] Every CTID/VMID with name, IP, resources
- [ ] What runs in CT 105 and CT 108

## Services
- [ ] **VM/CT ID for every Proxmox-hosted service** (Immich, TrueNAS, Paperless, NPM, AdGuard) — paste `pct list` + `qm list`
- [ ] Are the rewrites individual entries or one `*.andrims.net` wildcard? Which proxy hosts are public vs internal?
- [ ] Are Dozzle and Uptime Kuma already running (they have domains)?
- [ ] **LAN URL for every service** — see [Services → Quick links](services/index.md#quick-links)
- [ ] Vaultwarden: compose, URL, exposure, data path, **backup**
- [ ] Immich: VMID, LAN IP, deployment, how TrueNAS is mounted, DB backup
- [ ] TrueNAS: VMID, SCALE/CORE, disk passthrough method, pools, shares, snapshots, off-box copy
- [ ] Paperless-ngx: host, deployment, paths, export backup
- [ ] Jellyfin: Docker or native, paths, config backup
- [ ] Servarr: compose, download client, other apps, backup location
- [ ] Glance: config path, widgets
- [ ] Uptime Kuma: notification channel
