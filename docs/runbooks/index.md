# Runbooks

Step-by-step procedures. **Write each one the next time you actually do the task**, so the steps are real rather than guessed.

## Written

| Runbook | Why it matters | Status |
|---|---|---|
| [Move Immich from VM 107 to an LXC](immich-vm-to-lxc.md) | Frees 7 GB of pinned RAM, shares the iGPU, gets the DB into backups | 🟡 written from live config, not run yet |

## To write

| Runbook | Why it matters | Status |
|---|---|---|
| Restore Vaultwarden | Most important data in the lab; host is fragile | ❌ not written |
| Restore Immich (DB + library) | 1.6 TB library + metadata | ❌ not written |
| Restore Paperless-ngx | Documents | ❌ not written |
| Rebuild an LXC from backup | Any Proxmox guest | ❌ not written |
| Renew / fix certificates in NPM | Public Jellyfin + internal hosts | ❌ not written |
| GF65 after reboot / power cut | Docker Desktop needs a login | ❌ not written |
| Proxmox host updates | | ❌ not written |

Name files like `runbooks/restore-vaultwarden.md` and add them to `nav` in `mkdocs.yml`.
