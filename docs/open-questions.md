# Open questions

Everything marked ❓ in the docs, collected in one place. Tick them off as you verify.

## Hardware
- [ ] GF65: specs, where media is stored, auto-login set?, Jellyfin transcoding method (GPU or software)
- [ ] Monitoring Pi: Docker used for anything?

## Network
- [ ] UniFi: controller location, subnets/VLANs, DHCP reservations, AP model(s) (gateway = UX7, switch = USW-Flex-Mini-5-port — both confirmed)
- [ ] AdGuard: secondary DNS / redundancy if AdGuard itself goes down (upstream chain now confirmed: `1.1.1.1` → Quad9 DoH → `10.10.0.1`)
- [ ] NPM: host/CTID, IP, cert method (HTTP vs. Cloudflare DNS challenge), full proxy host list
- [ ] Cloudflare: full DNS record list; `andrims.com` — confirm if/how it's used (registrar + renewal date confirmed for both domains)
- [ ] DDNS updater (CT 109): confirm IP `10.10.0.109` (provider/target now confirmed: Cloudflare provider script → `aultmain.andrims.net`)
- [ ] Is `stream.andrims.net` a separate A record from `aultmain.andrims.net`, or does one point at the other? (ddns-updater confirmed to update `aultmain.andrims.net`, not `stream` directly — see [Cloudflare](network/cloudflare.md))
- [ ] Cloudflare Tunnel: tunnel name (runs on CT 102, `10.10.0.102` — IP now confirmed)
- [ ] Tailscale: MagicDNS, DNS settings, subnet router/exit node, device list

## Proxmox
- [ ] Version, node name, storage pools
- [ ] Backup schedule and destination
- [ ] Every CTID/VMID with name, IP, resources
- [ ] Confirm the *(inferred)* IPs in the [IP / CTID table](proxmox/ip-ctid-table.md): CT 102, 105, 109 (CT 105 is short-lived — being folded into the new "docker" CT — so lower priority)
- [ ] Confirm whether `immich.andrims.net` and `vault.andrims.net` share one `cloudflared` tunnel (CT 102) or run as two separate tunnels
- [ ] New "docker" CT: pick an ID/IP when it's created, migrate Paperless-ngx off CT 105, then delete CT 100, 103, 105, 108

## Services
- [ ] Confirm `pct list` / `qm list` output against the [IP / CTID table](proxmox/ip-ctid-table.md) (deployment method, resources, and the *(inferred)* IPs)
- [ ] Are the rewrites individual entries or one `*.andrims.net` wildcard? Which proxy hosts are public vs internal?
- [ ] Is Uptime Kuma already running (it has a domain, [uptime.andrims.net](https://uptime.andrims.net))? (Dozzle confirmed running.)
- [ ] **LAN URL for every service** — see [Services → Quick links](services/index.md#quick-links)
- [ ] Vaultwarden: compose, data path, **backup** (URL and exposure now confirmed — see its page)
- [ ] Immich: how TrueNAS is mounted, DB backup, deployment method inside VM 107
- [ ] TrueNAS (VM 104): SCALE/CORE version, pool/dataset layout on the single 4 TB disk, snapshot schedule, **off-box copy (no redundancy — priority)**
- [ ] Paperless-ngx (CT 105): deployment method, paths, export backup
- [ ] Jellyfin: Docker or native, paths, config backup
- [ ] Servarr: compose, download client, other apps, backup location
- [ ] Glance: config path, widgets
- [ ] Uptime Kuma: notification channel
- [ ] ActualBudget (CT 110): confirm IP, port, domain proxy host, backup
