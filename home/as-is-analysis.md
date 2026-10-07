# Home Lab - As-Is Analysis

Narrative snapshot of where we are today. For capacity/limits see `docs/resources.md`. For the
plan see `docs/implementation-plan.md` (the single phase plan).

## Context

- Two adults, both work from home. A 15-month-old son; a daughter due when he is ~19 months.
- Family touch points are **phones and old laptops only**. All Linux boxes are **headless servers**.
- Guiding belief: invest time up front to get more time back later. Anything that costs more time
  than it returns is a net negative, even if it is technically impressive.

## Network (as-is)

- Fios fiber -> **Firewalla Purple** (firewall, DHCP, VPN, DNS, ad-block).
- 2.5 GB wired backbone; **ASUS XD5** WiFi 6 access points.
- Planned (deferred): higher-throughput Firewalla replacement without external cloud management;
  WiFi 8 APs when affordable. See ADR 0004.

## Devices (as-is)

| Name | Device | Runs today |
|------|--------|-----------|
| sirius | Pi 5, 8 GB, ext SSD | getArcane, Traefik, Filebrowser |
| altair | Pi 4, 4 GB, ext SSD | historylabs.dev (zensical; rebuilds on redeploy) |
| vega | Pi 4, 2 GB, ext SSD | atlasofpelvicsurgery.org / .com / .net |
| pi3b | Pi 3B+, SD | offline |
| pi-zero-* | Pi Zero 2W x several | unused |
| charon | Asus ZenBook Pro UX501vw, 16 GB, NVMe, Intel iGPU + Nvidia 960M | Jellyfin (media on Synology, cache/metadata local), webtop (shares iGPU with Jellyfin), idle Minecraft/Terraria containers |
| jade | Fractal Terra: 9800X3D, 64 GB DDR5, AMD 9070 XT 16 GB, 800 W | Windows 11 daily driver (both adults), AI testing |
| methuselah | Synology 1621+, 32 GB, 32 TB + 1 TB NVMe cache | Synology services only (restricted Docker) |

Also present: a Steam Machine (not suitable), assorted Arduino Unos.

## Software (as-is)

- **getArcane** - management/orchestration dashboard, plus its own agent on every Linux host.
- **Traefik** - reverse proxy with a hot-reload file provider (add a YAML, route appears).
- **Filebrowser** - web file editor (used to edit Traefik files visually).
- **Dozzle** - container log viewer on every Linux host.
- **Jellyfin** - media server on charon.
- **Docker** - on all Linux hosts; Synology Docker is restricted.

## What already works in our favour

- Traefik hot-reload (drop a file -> route live).
- Arcane API + built-in container update management (**so no Watchtower**).
- Jellyfin already deployed and functional.
- Network-wide ad-blocking already handled by Firewalla (**so no Pi-hole needed**).
- SSD storage on the Pis; large Synology pool; strong GPU on jade.

## Gaps (what is missing)

- No infrastructure-as-code / desired state in Git (config is scattered).
- No service templates or automated deployment path.
- No backup **verification** process (backups may exist but are untested).
- No observability (no uptime/health dashboard, no alerting).
- GitHub automation cannot write to the intended dedicated repo from this sandbox (see `docs/migration.md`).
- Mixed architectures (ARM64 Pis vs x86_64) - every Pi-bound image must have an ARM64 build.

## Goals

### Primary goal (near term)

Make the home lab **pay back time**: improve reliability, privacy, and cost, and remove manual
chores, using the hardware we already own. Every change must clear the Invisible Value Test
(`service-catalog.md`).

### Stretch goal (multi-year, seeded, gated)

An AI-assisted lab where a plain-language request can produce a deployed service. This is
**not** a phase objective (ADR 0003). It is seeded now with small, useful pieces: structured
logging, machine-readable service metadata, templated deployments. For now only very
lightweight models may run on the always-on charon or a Pi; jade is NOT a network AI node and
large-model capability is deferred.

## Direction

`Git -> desired-state.yaml -> Arcane sync -> Traefik route -> service live`.

Full sequencing and exit criteria live in `docs/implementation-plan.md`. Do not add services
before Phase 0 is complete.
