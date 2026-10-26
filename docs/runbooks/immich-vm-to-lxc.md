# Runbook: move Immich from VM 107 to an LXC

**Status:** 🟡 planned, not done yet. Written 2026-10-26 from the live config; re-check the "Before you start" facts on the day.

## Why

| | VM 107 today | LXC after |
|---|---|---|
| RAM | 7 GB **pinned** on the host (PCI passthrough stops ballooning), guest only sees 6 GB | Limit is a ceiling; uses what it needs (~3 GB today) |
| iGPU (QuickSync) | Whole HD 530 owned by one VM | `/dev/dri/renderD128` shared — Immich, and later Jellyfin, can use it at the same time |
| Backups | Postgres disk is `backup=0`, so vzdump would skip the database | Database on the CT's root disk, included in vzdump |

## Target

| | |
|---|---|
| **CTID / hostname** | **111** / `immich` |
| **IP** | `10.10.0.111/24`, gateway `10.10.0.1` (follows the CTID convention) |
| **OS** | Debian 13, **unprivileged**, `nesting=1,keyctl=1` (Docker needs both) |
| **Resources** | 4 cores / 6 GB RAM / 512 MB swap / 40 GB root disk on `local-lvm` |
| **Library** | TrueNAS NFS share mounted **on the Proxmox host**, bind-mounted into the CT at the same path, `/mnt/immich-data` |
| **Database** | `/opt/immich/postgres` on the CT's root disk |
| **App** | Same Docker Compose stack as now, in `/opt/immich` |
| **Starts** | After TrueNAS: `startup: order=2,up=30` |

Why Docker in an LXC and not the community helper script's native Immich: the current stack, `.env` and database move over unchanged, and Immich upgrades stay `docker compose pull`. This is a deliberate exception to [rule 1](../roadmap.md#standing-rules); note it on the Immich page.

## Before you start

Facts this runbook relies on (checked 2026-10-26):

- VM 107 compose dir: `/root/docker/immich-docker-ct/` (`docker-compose.yml`, `.env`, `hwaccel.transcoding.yml`, `hwaccel.ml.yml`, `var/`).
- `.env`: `UPLOAD_LOCATION=/mnt/immich-data/immich`, `DB_DATA_LOCATION=/mnt/immich-db-scsi-vdisk/postgres`, `IMMICH_VERSION=release`.
- External library: `/mnt/immich-data/aadil-photography` → `/photos:ro`.
- NFS export: `10.10.0.22:/mnt/photo-storage/immich-data`. `immich/` is owned by UID 1000, Postgres data by UID 999.
- The iGPU is only taken by VM 107's `hostpci0`; it isn't pinned to `vfio-pci` in `/etc/modprobe.d/` (only the SATA controller is).
- Public path: Cloudflare Tunnel on CT 102 → `http://10.10.0.107:2283`.

Checklist:

- [ ] Pick a quiet evening; Immich is down from step 5 to step 8 (~30–60 min).
- [ ] Note the exact running version: Immich web UI → Administration → Server info (it was **v3.3.5**). The new stack will be pinned to the same version for the move.
- [ ] Back up VM 107. Its database disk is `backup=0`, so take the DB separately too:
    ```bash
    # on PVE-7050 — VM backup (boot disk only, because scsi1 is backup=0)
    vzdump 107 --mode snapshot --compress zstd --storage local
    # on VM 107 — fresh DB dump next to the nightly ones
    docker exec -t immich_postgres pg_dumpall --clean --if-exists --username=postgres \
      | gzip > /mnt/immich-data/immich/backups/manual-pre-lxc-$(date +%F).sql.gz
    ```
- [ ] Copy the Immich `.env` somewhere safe off the VM (it holds `DB_PASSWORD`; keep it out of this repo — Vaultwarden "Immich DB").

## 1. TrueNAS: let the Proxmox host mount the share

The CT is unprivileged, so its root (UID 0) shows up on the host as UID 100000. Instead of fighting UID mapping, have TrueNAS squash every client write to the owner of the library.

1. TrueNAS → Shares → NFS → the `immich-data` share → Edit. **Write down the current settings first.**
2. Authorized hosts/networks: make sure `10.10.0.15` (PVE-7050) is allowed, alongside `10.10.0.107` for now.
3. Advanced → **Mapall User** = the user with UID 1000 (the owner of `immich/`), **Mapall Group** = its group.
4. Save.

!!! warning
    Mapall affects VM 107 too, but it already writes as root to a directory owned by 1000, so mapping everything to 1000 is consistent. Test after step 2 by touching a file in `immich/` from the host and checking its owner.

## 2. Proxmox host: mount the share

```bash
# on PVE-7050
apt install -y nfs-common        # may already be there
mkdir -p /mnt/immich-data
echo '10.10.0.22:/mnt/photo-storage/immich-data /mnt/immich-data nfs rw,nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noatime,_netdev,x-systemd.automount 0 0' >> /etc/fstab
systemctl daemon-reload
ls /mnt/immich-data               # triggers the automount
touch /mnt/immich-data/immich/.lxc-test && stat -c '%u:%g' /mnt/immich-data/immich/.lxc-test && rm /mnt/immich-data/immich/.lxc-test
```

`x-systemd.automount` matters: TrueNAS is a VM on this same host, so at boot the share doesn't exist yet. The automount waits until something touches the path.

## 3. Create CT 111

Either a community helper script **Docker** LXC (Advanced: CTID 111, hostname `immich`, static IP, Debian 13, unprivileged, sizes above), or by hand:

```bash
pct create 111 local:vztmpl/debian-13-standard_*.tar.zst \
  --hostname immich --cores 4 --memory 6144 --swap 512 \
  --rootfs local-lvm:40 --unprivileged 1 --features nesting=1,keyctl=1 \
  --net0 name=eth0,bridge=vmbr0,ip=10.10.0.111/24,gw=10.10.0.1 \
  --nameserver 10.10.0.53 --timezone America/New_York \
  --onboot 1 --startup order=2,up=30 --tags immich
pct set 111 --mp0 /mnt/immich-data,mp=/mnt/immich-data,backup=0
pct start 111
pct exec 111 -- bash -c 'apt update && apt install -y curl rsync && curl -fsSL https://get.docker.com | sh'
```

`backup=0` on `mp0` keeps vzdump from trying to back up 1.6 TB of NFS.

Check from inside: `pct exec 111 -- ls /mnt/immich-data/immich` lists `library`, `upload`, `thumbs`, …

## 4. Prepare the stack in the CT (Immich still running on VM 107)

```bash
# inside CT 111
mkdir -p /opt/immich/postgres
cd /opt/immich
scp root@10.10.0.107:/root/docker/immich-docker-ct/{docker-compose.yml,.env,hwaccel.transcoding.yml,hwaccel.ml.yml} .
```

Edit `/opt/immich/.env`:

```diff
-DB_DATA_LOCATION=/mnt/immich-db-scsi-vdisk/postgres
+DB_DATA_LOCATION=/opt/immich/postgres
-IMMICH_VERSION=release
+IMMICH_VERSION=v3.3.5      # same version as VM 107 for the move; back to release afterwards
```

`UPLOAD_LOCATION` and the `/photos` mount stay the same, because the share is at the same path.

ML balancer: all three remote workers (`server-gf65`, `ault-ge68`, `10.10.0.140`) were unreachable on 2026-10-26, so the simplest move is to **drop `immich-ml-balancer`** from the compose file and point the server at the local ML container (Administration → Settings → Machine Learning → URL `http://immich-machine-learning:3003`). Keep it only if you plan to run those workers.

Pull images ahead of time so the downtime is short:

```bash
docker compose pull
```

## 5. Cutover: stop Immich and copy the database

```bash
# on VM 107
cd /root/docker/immich-docker-ct && docker compose down
```

Cold-copy the Postgres data directory (same Postgres image on both sides, so this is an exact copy):

```bash
# inside CT 111
rsync -aHAX --numeric-ids --info=progress2 \
  root@10.10.0.107:/mnt/immich-db-scsi-vdisk/postgres/ /opt/immich/postgres/
```

`--numeric-ids` keeps the files owned by UID 999 inside the CT, which is what the Postgres container expects. (~2.6 GB.)

If the copy is ever in doubt, use the restore from dump instead: follow Immich's [backup and restore docs](https://immich.app/docs/administration/backup-and-restore) with the `manual-pre-lxc-*.sql.gz` dump from "Before you start".

## 6. Move the iGPU from the VM to the host

```bash
# on PVE-7050
qm shutdown 107 && qm wait 107
qm set 107 --delete hostpci0
qm set 107 --onboot 0
# hand 00:02.0 back to i915 without a host reboot
echo 0000:00:02.0 > /sys/bus/pci/drivers/vfio-pci/unbind
modprobe i915
ls -l /dev/dri                      # expect card0 + renderD128
```

If `/dev/dri` doesn't appear, reboot the host (every guest restarts; TrueNAS comes up first by its startup order).

Give the render node to the CT. Find the `render` group's GID **inside the CT** first (Debian 13 is usually 993 — check):

```bash
pct exec 111 -- getent group render
pct set 111 --dev0 /dev/dri/renderD128,gid=<render-gid-in-CT>
pct reboot 111
```

## 7. Start Immich in the CT

```bash
# inside CT 111
cd /opt/immich && docker compose up -d
docker compose logs -f immich-server    # wait for "Immich Server is listening"
```

Check:

- [ ] [http://10.10.0.111:2283](http://10.10.0.111:2283) logs in; albums, people and the external library (`/photos`) are all there.
- [ ] Upload a short phone video, then Administration → Jobs shows the transcode finishing. On the host, `apt install intel-gpu-tools` then `intel_gpu_top` shows the **Video** engine busy during the transcode.
- [ ] Administration → Jobs: no failed jobs piling up.
- [ ] The nightly DB backup still targets `/mnt/immich-data/immich/backups` (Administration → Settings → Backup).

## 8. Point everything at the new IP

- [ ] **Cloudflare Tunnel** (CT 102): change the `immich.andrims.net` public hostname's service from `http://10.10.0.107:2283` to `http://10.10.0.111:2283` (Zero Trust dashboard → Networks → Tunnels, or the tunnel's config file if it's locally managed). Test from mobile data.
- [ ] Phone apps: nothing to change if they use `immich.andrims.net`; any app set to the LAN URL needs `10.10.0.111`.
- [ ] **Uptime Kuma:** update the Immich LAN check.
- [ ] **Dozzle:** add a `dozzle-agent` to the CT (compose in [Dozzle](../services/dozzle.md)), add `10.10.0.111:7007` to `DOZZLE_REMOTE_AGENT`, remove `10.10.0.107`.
- [ ] **Glance:** update any Immich link/widget.
- [ ] **Tailscale:** VM 107 was on the tailnet as `vm-107`. Only add Tailscale to the CT if something needs it (it would also need `/dev/net/tun`, like CT 105).
- [ ] Set `IMMICH_VERSION=release` again in `.env` once happy, then `docker compose pull && docker compose up -d`.

## 9. Rollback (if step 7 fails)

```bash
# inside CT 111
cd /opt/immich && docker compose down
# on PVE-7050
pct set 111 --delete dev0
qm set 107 --hostpci0 0000:00:02 --onboot 1
qm start 107                        # vfio-pci takes the iGPU back
# on VM 107
cd /root/docker/immich-docker-ct && docker compose up -d
```

VM 107's database disk is untouched by this runbook, so it comes back exactly as it was at step 5. Anything uploaded to the CT in the meantime is on the share but won't be in VM 107's database. Set the Cloudflare Tunnel back to `.107` if you already changed it. Undo the TrueNAS mapall only if it caused the problem.

## 10. Clean up (after ~2 weeks of the CT running fine)

- [ ] Add CT 111 to the vzdump job (once one exists — see [Proxmox host](../proxmox/host.md)) and test a restore.
- [ ] Delete VM 107 and its disks (`qm destroy 107 --purge`). This also frees its 20 + 32 GB on `local-lvm`.
- [ ] Remove `10.10.0.107` from the TrueNAS share's allowed hosts.
- [ ] Remove the old `vm-107` device from the Tailscale admin console.
- [ ] Docs, in the same commit, with a [changelog](../changelog.md) entry: [IP / CTID table](../proxmox/ip-ctid-table.md) (CT 111 in, VM 107 out), [Immich](../services/immich.md), [Proxmox host](../proxmox/host.md) resource picture (−7 GB of pinned VM RAM), [OptiPlex](../hardware/optiplex-7050.md) iGPU line, [Services](../services/index.md) LAN link, [network map](../index.md), [Cloudflare](../network/cloudflare.md), [Tailscale](../network/tailscale.md), and mark this runbook done.

## Later: Jellyfin on the same iGPU (or a new GPU)

Once the iGPU belongs to the host, a Jellyfin LXC can get the same `dev0: /dev/dri/renderD128,gid=…` line and both containers transcode on it at once. A low-profile Intel Arc card added later shows up as a second render node (`renderD129`) and is passed the same way.
