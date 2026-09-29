# Dozzle

> **What / why:** One place to read live logs from every container on every host, so errors are easy to find when something breaks.

**Status:** ✅ running — main instance confirmed up on the [Monitoring Pi](../hardware/monitoring-pi.md) (`10.10.0.6`). The agent on [Server-GF65](../hardware/msi-gf65.md) (`dozzle-agent`) is ✅ confirmed running. ❓ Confirm the agent on CT 105 (`docker`).

!!! note "Dozzle ≠ Dockge"
    **Dozzle** is for *watching* containers (logs). **Dockge** is for *managing* compose stacks, and doesn't support Windows. Dozzle's agent does work on Docker Desktop for Windows.

## Architecture

- **Main Dozzle** on the [Monitoring Pi](../hardware/monitoring-pi.md).
- **A Dozzle agent** on every Docker host: the MSI GF65 (Docker Desktop), and **CT 105 (`docker`)**, the shared Docker host on Proxmox (`10.10.0.105`). The old Docker LXCs (CT 103, 108) have been deleted.
- Use **Tailscale IPs** for the agents where possible, instead of opening 7007 on the LAN.

## Compose — agent (each Docker host, incl. GF65)

```yaml
services:
  dozzle-agent:
    image: amir20/dozzle:latest
    command: agent
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    ports:
      - 7007:7007
    restart: unless-stopped
```

On the GF65, allow port 7007 through Windows Firewall (or use Tailscale).

## Compose — main (Monitoring Pi)

```yaml
services:
  dozzle:
    image: amir20/dozzle:latest
    environment:
      - DOZZLE_REMOTE_AGENT=<agent1-ip>:7007,<agent2-ip>:7007
    ports:
      - 8080:8080
    restart: unless-stopped
```

## Rollout order

1. GF65 agent first — it's the one most likely to fight you (firewall, socket path).
2. Then the Linux Docker hosts.
3. Update the [IP / CTID table](../proxmox/ip-ctid-table.md) and [changelog](../changelog.md).
