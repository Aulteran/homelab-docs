# Glance

> **What / why:** Dashboard for the whole homelab. Built with Claude Code.

**Status:** ✅ running

## Where

| | |
|---|---|
| **Host** | [Monitoring Pi](../hardware/monitoring-pi.md) |
| **Type** | ❓ Docker container / binary |
| **LAN IP / port** | `10.10.0.6` : 8080 |
| **Domain URL** | [dash.andrims.net](https://dash.andrims.net) |
| **LAN URL** | [http://10.10.0.6:8080](http://10.10.0.6:8080) |

## Config

- ❓ Where `glance.yml` lives on the Pi.
- Consider committing the config (secrets stripped) to this repo or its own repo on Forgejo.

## What it shows

- ❓ Proxmox host stats (CPU, RAM incl. ZFS ARC, swap, disk)? — confirm which dashboard shows these
- ❓ other widgets / service links

## To add

- Forgejo, once it's deployed.
- Docs site, once it's deployed.
