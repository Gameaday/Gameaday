# To-Be Architecture (Headless-First)

## The key fact

Every Linux box is a **headless server** - no monitor, keyboard, or touchscreen. They are the
digital spine, not family touch points. The only things the family touches are **phones and old
laptops**. So value must be delivered *invisibly*: things get faster, cheaper, more private, or
more reliable without anyone logging into a server.

## Core philosophy

Headless services earn their place by improving **reliability, privacy, cost, or time** for the
family - without requiring interaction with the servers. The best service is one nobody thinks
about because it just works.

## Guiding principles

1. **Invisible value** - if family notices it at all, it is because it broke.
2. **Privacy by architecture** - data stays home unless there is a compelling reason.
3. **Replace recurring costs** - target subscriptions where a self-hosted equivalent is solid.
4. **Reliability multiplier** - improve the services people actually use.
5. **Zero-touch** - aim for <5 min/month maintenance, averaged.
6. **Graceful degradation** - lab down does not mean family life disrupted.
7. **Time-positive** - if it costs more time than it returns, it does not ship.

## Device roles (target)

| Device | Role |
|--------|------|
| sirius (Pi 5, 8 GB) | Orchestration: Arcane, Traefik, Filebrowser, n8n, observability. **No Pi-hole, no LLM.** |
| altair (Pi 4, 4 GB) | Reliability worker: apt-cacher-ng, site deploy/verify, photo queue (best-effort). |
| vega (Pi 4, 2 GB) | Medical sites + Jitsi TURN/STUN. Tight RAM - single purpose. |
| pi-zero-* | Environmental sensors (temp/humidity/leak). Alert-only. |
| charon (ZenBook) | Media engine: Jellyfin with Intel QuickSync. On-demand, not 24/7. |
| jade (Fractal Terra) | Adult workstation; occasional AI only, must not disrupt work. GPU accel unproven (AMD/Windows). |
| methuselah (Synology) | Storage spine: snapshots, offsite archive, media + photos. |

## Target architecture (text)

**Storage spine** - Synology holds encrypted snapshots and the offsite archive, plus organized
media and photos. Irreplaceable data must exist in 3 places (origin, Synology, offsite).

**Orchestration** - sirius runs Arcane + Traefik + n8n and is where monitoring/alerting originate.
No Pi-hole (Firewalla owns DNS/ad-block, ADR 0004). No local LLM (8 GB ARM cannot; see
`docs/resources.md`).

**Workers** - altair caches packages and runs a best-effort photo queue; vega serves medical sites
and TURN/STUN; Pi Zeroes are pure sensors. Each is small and single-purpose.

**Media** - charon transcodes with QuickSync, on-demand to keep heat/power/noise down.

**AI (stretch, gated)** - jade only, occasional, non-disruptive. Gated on proving GPU
acceleration works on AMD/Windows or on a future dedicated node (ADR 0003).

**Family interface** - phones and old laptops. Alerts land in the family chat; everything else is
invisible when healthy.

## Design rules

1. Services are invisible when healthy; they surface only via actionable alerts.
2. One management plane (Arcane), one proxy (Traefik) - ADR 0002.
3. No service without clearing the Invisible Value Test (`service-catalog.md`).
4. Nothing depends on the AI stretch goal.
5. Every Pi-bound image must have an ARM64 build.

## Relationship to other docs

- Capacity/limits: `docs/resources.md`
- Sequencing + exit criteria: `docs/implementation-plan.md`
- Decisions: `docs/adr/`
- Current state: `as-is-analysis.md`
