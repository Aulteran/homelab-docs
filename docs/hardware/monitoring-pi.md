# Monitoring Pi

A Raspberry Pi dedicated **only** to monitoring the rest of the homelab. Nothing else goes on it.

| | |
|---|---|
| **Model / RAM** | Raspberry Pi 5 Model B Rev 1.0 — 4 GB RAM |
| **OS** | Debian GNU/Linux 13 (trixie), aarch64 — kernel `6.18.29+rpt-rpi-2712` |
| **Storage** | 64 GB microSD card (`SD64G`), 9.5 GB used of 58 GB on root (2026-10-25). Root is mounted `noatime`. |
| **Swap** | 2 GB **zram** (compressed RAM, so no swap writes to the SD card) |
| **LAN IP** | `10.10.0.6` |
| **Hostname** | `raspberrypi` (default, never changed). On the tailnet and in the SSH config as `monitoring-pi`. |
| **SSH user** | `ault` |
| **Tailscale IP** | `100.101.228.37` |
| **Docker?** | Yes — everything below runs as a Docker container. No `/etc/docker/daemon.json`, so container logs use the default `json-file` driver **with no size limit**, on the SD card. |
| **Temperature** | ~65 °C at idle, no throttling (`throttled=0x0`) on 2026-10-25 |
| **Updates** | `unattended-upgrades` is not installed |

## What runs here

| Service | Port | Status |
|---|---|---|
| [Glance](../services/glance.md) | `8080` | ✅ running (container `glance`) |
| [Dozzle](../services/dozzle.md) (main instance) | `8888` → 8080 | ✅ running (container `dozzle`) |
| [Uptime Kuma](../services/uptime-kuma.md) | `3001` | ✅ running (container `uptime-kuma-uptime-kuma-1`) |
| Homepage | `3000` | 🟠 running (container `homepage`), **to be decommissioned** — Glance replaces it |
| Prometheus + Grafana | — | 🟡 planned — **blocked, see rule below** |

The Pi kernel boots with `cgroup_disable=memory` (Raspberry Pi OS default), so `docker stats` shows 0 B memory and container memory limits don't work.

!!! danger "Rule: no Prometheus or Grafana on the microSD card"
    Don't add Prometheus or Grafana to this Pi until its storage is moved off the microSD card. Prometheus is write-heavy and will wear out an SD card. A **USB SSD** is the better target; a plain USB stick is better than SD but still not great.

    Lighter alternative to consider first: **Beszel** (agent-based, works on Windows too) for per-host and per-container CPU/RAM/disk.
