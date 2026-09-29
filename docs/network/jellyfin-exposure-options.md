# Jellyfin exposure options

> **What / why:** Ways to make Jellyfin reachable from outside without publishing the home IP. The [edge VPS](edge-vps.md) is the current plan; this page records the alternatives and what each costs, so the choice can be revisited.

Prices and limits below were checked on **2026-09-28** from the linked sources. Cloud prices change often (Hetzner raised its prices twice in 2026), so re-check before buying.

## Where things stand today

`stream.andrims.net` → CNAME `aultmain.andrims.net` → home IP → UniFi port-forward → NPM (CT 101) → Jellyfin. This is the only place the home IP is published. See [Cloudflare → Home IP exposure](cloudflare.md#home-ip-exposure).

## Options at a glance

| # | Option | Public URL stays `stream.andrims.net`? | Home IP published? | Cost | Main catch |
|---|---|---|---|---|---|
| A | [Edge VPS + NPM over Tailscale](#a-edge-vps-npm-over-tailscale-current-plan) (current plan) | ✅ | ❌ (VPS IP instead) | $0–$7/mo | You run and patch an internet-facing VM. |
| B | [Edge VPS + Pangolin or plain WireGuard](#b-edge-vps-pangolin-or-plain-wireguard) | ✅ | ❌ | Same VPS cost, $0 software | Less familiar than NPM. Smaller blast radius than A. |
| C | [Cloudflare Tunnel for Jellyfin](#c-cloudflare-tunnel-for-jellyfin) | ✅ | ❌ | $0 | Terms-of-service grey zone for video. |
| D | [Tailscale Funnel](#d-tailscale-funnel) | ❌ (`*.ts.net` only) | ❌ | $0 | Non-configurable bandwidth limits. |
| E | [Pangolin Cloud (hosted)](#e-pangolin-cloud-hosted) | ✅ (custom domains) | ❌ | $0 on the free plan | Video-traffic policy unverified. |
| F | [Private only: Tailscale, no public URL](#f-private-only-tailscale-no-public-url) | ❌ | ❌ | $0 | Every viewer needs Tailscale. |

## A. Edge VPS + NPM over Tailscale (current plan)

Full design on the [Edge VPS](edge-vps.md) page. Cloud VM with NPM, joined to the tailnet, forwards to Jellyfin over Tailscale. Home ports 80/443 get closed.

- **Cost:** see [VPS providers and budgets](#vps-providers-and-budgets).
- **Good:** keeps the familiar NPM setup, works with any Jellyfin client, the domain doesn't change.
- **Catch:** a VM on your tailnet is a bigger target than a VM that only holds one tunnel (compare B). Needs a strict Tailscale ACL.

## B. Edge VPS + Pangolin or plain WireGuard

Same VPS, but the VM isn't a tailnet member. Instead it holds one dedicated tunnel to the GF65:

- **[Pangolin](https://pangolin.net)** (self-hosted Community Edition is free): a tunneled reverse proxy with a WireGuard agent (Newt) on the home side, a web UI, and optional built-in authentication in front of resources.
- **Plain WireGuard, or frp / rathole:** the same idea with fewer moving parts and no UI: nginx or Caddy on the VPS, one WireGuard tunnel to the GF65.

- **Cost:** the same VPS as A. Software is free.
- **Good:** if the VM is compromised, the attacker reaches only what's inside that one tunnel (Jellyfin), not your tailnet. The VM also doesn't need an ACL written in Tailscale.
- **Catch:** a second remote-access system to learn next to Tailscale. Pangolin's optional login page in front of a resource would likely get in the way of native Jellyfin apps (TVs, phones), so expect to leave it off for Jellyfin and rely on Jellyfin's own login. (Not tested.)

## C. Cloudflare Tunnel for Jellyfin

Put `stream.andrims.net` on the existing Cloudflare Tunnel (`cloudflared`, CT 102), like Vaultwarden and Seerr.

- **Cost:** $0. No new machines.
- **Good:** the easiest change of all, and it removes the last home-IP record.
- **Catch:** Cloudflare's terms have historically restricted serving video through its proxy. A 2023 rewrite made the wording ambiguous. Reports differ: one article says staff have said household use is fine, while community members point at the terms and say it technically violates them. Enforcement has mostly targeted heavy public use, but accounts do sometimes get warned or throttled. Cloudflare doesn't clearly permit it, and it could stop working, so this puts your domain's Cloudflare account at some risk.
- **Sources:** [LumaDock](https://lumadock.com/tutorials/cloudflare-tunnel-jellyfin), [Cloudflare Community thread](https://community.cloudflare.com/t/confusion-about-tos-in-relation-to-using-cloudflared-tunnel-for-media-streaming/839900).

!!! warning "Don't do this on the account that holds your domain"
    If Cloudflare ever restricted the account, the same account holds `andrims.net` DNS and the registrar. That's a larger loss than losing Jellyfin.

## D. Tailscale Funnel

`tailscale funnel` publishes a local service on the internet through Tailscale's relays.

- **Cost:** $0 (available on all plans).
- **Good:** no VM, no port forward, no domain setup.
- **Catch:** traffic is subject to **non-configurable bandwidth limits**, only ports 443, 8443 and 10000 are allowed, and you can only use `<machine>.<tailnet>.ts.net` names, not `stream.andrims.net`. Good for occasional sharing, poor for regular streaming.
- **Source:** [Tailscale Funnel docs](https://tailscale.com/docs/features/tailscale-funnel).

## E. Pangolin Cloud (hosted)

Pangolin also runs a hosted version, so there's no VPS to manage.

- **Cost:** Basic plan is **free** (up to 5 users, 5 sites, 5 domains). Team is $4/user/month, Business $9/user/month.
- **Good:** no VM to patch, keeps a custom domain.
- **Catch:** I couldn't find a published policy on sustained video traffic, so confirm it before relying on it. Your streaming would depend on their infrastructure.
- **Source:** [Pangolin pricing](https://pangolin.net/pricing).

## F. Private only: Tailscale, no public URL

Nothing public. Family and friends install Tailscale, or you [share the GF65 node](https://tailscale.com/kb/1084/sharing) with their tailnets, and open Jellyfin at its Tailscale address.

- **Cost:** $0 on Tailscale's free personal plan.
- **Good:** zero public attack surface, no IP to hide. The only option where Jellyfin isn't internet-facing at all.
- **Catch:** every viewer needs Tailscale, and TVs and streaming boxes often can't run it (a subnet router or a phone/laptop as a stand-in is needed). Not "public."
- **Source:** [Jellyfin docs: Tailscale](https://jellyfin.org/docs/general/post-install/networking/tailscale/).

## VPS providers and budgets

Options A and B need a small VM. Jellyfin video passes through it, so **network speed and monthly traffic matter more than CPU or RAM**. Two vCPUs and 1 GB RAM is plenty.

| Provider / plan | Location | Specs | Traffic | Price | Notes |
|---|---|---|---|---|---|
| **Oracle Cloud Always Free — Ampere A1** | Your home region (US East/Ashburn is available) | Up to 2 OCPU + 12 GB in total (the free allowance) | **10 TB/month outbound** | **$0** | Pick this shape, not the AMD micro. See the cautions below. |
| Oracle Always Free — AMD micro | Your home region | 1/8 OCPU, 1 GB | 10 TB/month, but **50 Mbps cap** | $0 | The 50 Mbps port limit is too low for more than a stream or two. |
| **BuyVM (FranTech) — Slice 1024** | New York, Las Vegas, Luxembourg | 1 core, 1 GB, 20 GB | **Unmetered** | **$3.50/mo** ($42/yr) | Best fit for a US East home if you can get one. Reviews say stock runs out often. |
| BuyVM — Slice 2048 | Same | 1 core, 2 GB, 40 GB | Unmetered | $7/mo | |
| **Hetzner CX23** | Germany / Finland only | 2 vCPU, 4 GB, 40 GB | 20 TB/month | **€5.49 (~$6.49)/mo**, +€0.50 for IPv4 | Prices rose 15 June 2026. From a US home, EU adds about 80–100 ms. |
| Hetzner CAX11 (Arm) | Germany / Finland | 2 vCPU, 4 GB, 40 GB | 20 TB/month | €5.99 (~$6.99)/mo | |
| Hetzner CPX11 | Ashburn / Hillsboro (US) | 2 vCPU | 1–8 TB (varies by plan) | $20.49/mo | The US plans now cost 3× the EU ones. |
| Budget annual deals (RackNerd and similar) | US | ~1 GB RAM | Varies by deal | Third-party sites report ~$10–25/yr | I couldn't check these on the provider's own page. Read the traffic and port-speed terms first. |

Sources: [Hetzner price adjustment](https://docs.hetzner.com/general/infrastructure-and-availability/price-adjustment/), [Hetzner pricing summary (Sep 2026)](https://costgoat.com/pricing/hetzner), [Oracle Always Free resources](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier_topic-Always_Free_Resources.htm), [BuyVM slices](https://buyvm.net/kvm-dedicated-server-slices/).

### How much traffic to plan for

One remote viewer watching 1080p at roughly 8 Mbps uses about 3.6 GB per hour. Streaming two hours a night for one viewer is about 220 GB a month. Reviews suggest a family that watches nightly lands in the 400–750 GB/month range. Every plan above allows that, except Hetzner's US plans at the low end.

### Oracle Free Tier cautions

- **Idle reclamation.** Oracle reclaims an Always Free instance if, over 7 days, CPU and network use are both below 20% (and memory below 20% on Ampere shapes). A reverse proxy that mostly sits idle can meet that. Upgrading the account to Pay As You Go is commonly reported to avoid this (the Always Free resources stay free), but I couldn't confirm that in Oracle's own docs. Either way, keep the config documented so the VM is quick to recreate.
- Accounts inactive for 30+ days may be suspended, and Oracle asks for a card to verify identity.
- One free account per person.

## Things every VPS option has in common

- **The VPS learns the home IP.** A direct WireGuard connection (Tailscale included) shows the home IP as the peer's address. The provider, or anyone who breaks into the VM, can see it. What you gain is that it's no longer in public DNS.
- **The VPS IP becomes the public one.** Keep it patched, SSH key-only, firewall closed except 80/443.
- **Move the last home-IP record.** Only after the VPS works: point `stream.andrims.net` at it, wait out the DNS TTL, then close ports 80/443 on the UX7.

## Quick recommendation

This is a summary of the trade-offs above, not a decision. It's your call.

- **Fastest and cheapest:** C (Cloudflare Tunnel), if you accept the terms risk, or F (private Tailscale) if the viewers are a few people you can set up.
- **Most robust while staying public:** B, on a **$3.50 BuyVM NY slice** or an Oracle **A1** instance.
- **Keep A** if you want to keep using NPM and its UI. Just lock the VM down with Tailscale ACLs.

## Decision

❓ Not made yet. Record the choice here and in the [changelog](../changelog.md) when it is.
