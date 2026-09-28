# Monitoring Pi

A Raspberry Pi dedicated **only** to monitoring the rest of the homelab. Nothing else goes on it.

| | |
|---|---|
| **Model / RAM** | ❓ |
| **OS** | ❓ |
| **Storage** | microSD card |
| **LAN IP** | ❓ |
| **Hostname** | ❓ |
| **Docker?** | ❓ |

## What runs here

| Service | Status |
|---|---|
| [Glance](../services/glance.md) | ✅ |
| [Dozzle](../services/dozzle.md) (main instance) | 🟡 planned |
| [Uptime Kuma](../services/uptime-kuma.md) | 🟡 planned |
| Prometheus + Grafana | 🟡 planned — **blocked, see rule below** |

!!! danger "Rule: no Prometheus or Grafana on the microSD card"
    Don't add Prometheus or Grafana to this Pi until its storage is moved off the microSD card. Prometheus is write-heavy and will wear out an SD card. A **USB SSD** is the better target; a plain USB stick is better than SD but still not great.

    Lighter alternative to consider first: **Beszel** (agent-based, works on Windows too) for per-host and per-container CPU/RAM/disk.
