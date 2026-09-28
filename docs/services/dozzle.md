# Dozzle (planned)

> **What / why:** One place to read live logs from every container on every host, so errors are easy to find when something breaks.

**Status:** 🟡 planned ❓ (domain [dozzle.andrims.net](https://dozzle.andrims.net) exists in NPM — running already?)

!!! note "Dozzle ≠ Dockge"
    **Dozzle** is for *watching* containers (logs). **Dockge** is for *managing* compose stacks, and doesn't support Windows. Dozzle's agent does work on Docker Desktop for Windows.

## Architecture

- **Main Dozzle** on the [Monitoring Pi](../hardware/monitoring-pi.md).
- **A Dozzle agent** on every Docker host: CT 105, CT 108, other Docker LXCs/VMs, and the MSI GF65.
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
