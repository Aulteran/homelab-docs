# Open questions

Everything marked ❓ in the docs, collected in one place. Tick them off as you verify.

## Hardware
- [ ] OptiPlex: RAM total, disks, ZFS pool layout, NICs, IP
- [ ] GF65: specs, IP, where media is stored, auto-login set?
- [ ] Monitoring Pi: model, OS, IP, Docker?
- [ ] MacBook Air: how it does Jellyfin transcoding (own Jellyfin instance vs. remote transcoder), how it reaches media, sleep handling

## Network
- [ ] UniFi models, controller location, subnets/VLANs, DHCP reservations
- [ ] Port forwards (443/80 → NPM?)
- [ ] AdGuard: host/CTID, IP, upstreams, secondary DNS
- [ ] NPM: host/CTID, IP, cert method (HTTP vs. Cloudflare DNS challenge), full proxy host list
- [ ] Cloudflare: registrar, renewal date, DDNS method, full record list
- [ ] Tailscale: MagicDNS, DNS settings, subnet router/exit node, device list

## Proxmox
- [ ] Version, node name, storage pools
- [ ] Backup schedule and destination
- [ ] Every CTID/VMID with name, IP, resources
- [ ] What runs in CT 105 and CT 108

## Services
- [ ] Vaultwarden: compose, URL, exposure, data path, **backup**
- [ ] Immich: VMID, deployment, how TrueNAS is mounted, DB backup
- [ ] TrueNAS: VMID, SCALE/CORE, disk passthrough method, pools, shares, snapshots, off-box copy
- [ ] Paperless-ngx: host, deployment, paths, export backup
- [ ] Jellyfin: Docker or native, paths, config backup
- [ ] Servarr: compose, download client, other apps, backup location
- [ ] Glance: config path, widgets
- [ ] Uptime Kuma: notification channel
