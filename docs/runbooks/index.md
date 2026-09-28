# Runbooks

Step-by-step procedures. **Write each one the next time you actually do the task**, so the steps are real rather than guessed.

## To write

| Runbook | Why it matters | Status |
|---|---|---|
| Restore Vaultwarden | Most important data in the lab; host is fragile | ❌ not written |
| Restore Immich (DB + library) | 900 GB library + metadata | ❌ not written |
| Restore Paperless-ngx | Documents | ❌ not written |
| Rebuild an LXC from backup | Any Proxmox guest | ❌ not written |
| Renew / fix certificates in NPM | Public Jellyfin + internal hosts | ❌ not written |
| GF65 after reboot / power cut | Docker Desktop needs a login | ❌ not written |
| Proxmox host updates | | ❌ not written |

Name files like `runbooks/restore-vaultwarden.md` and add them to `nav` in `mkdocs.yml`.
