# Home Lab Resources & Constraints

Single source of truth for hardware capacity, roles, and limits. `as-is-analysis.md` tells the
story; this file is the engineering reference. Update this whenever hardware changes.

## Network

| Item | Detail |
|------|--------|
| WAN | Fios fiber |
| Firewall / DHCP / DNS / ad-block | Firewalla Purple (also VPN) |
| Wired | 2.5 GB Ethernet backbone |
| Wireless | ASUS XD5 access points (WiFi 6) |
| Planned | Replace Firewalla with higher-throughput, no-external-cloud option; WiFi 8 APs when affordable |

**Implication**: Firewalla already owns DNS + ad-blocking. Do **not** deploy a second DNS/ad
blocker (e.g. Pi-hole) unless Firewalla is proven insufficient. Local *caching* proxy is still
useful and non-overlapping.

## Devices

| Name | Hardware | Storage | GPU | Role (current) | Role (target) | Uptime |
|------|----------|---------|-----|----------------|---------------|--------|
| **sirius** | Pi 5, 8 GB | external SSD | - | getArcane, Traefik, Filebrowser | Orchestration brain + proxy + monitoring | 24/7 |
| **altair** | Pi 4, 4 GB | external SSD | - | historylabs.dev site | Site deploy/verify + apt-cacher-ng + photo queue | 24/7 |
| **vega** | Pi 4, 2 GB | external SSD | - | atlasofpelvicsurgery.org sites | Medical sites + Jitsi TURN/STUN (light) | 24/7 |
| **pi3b** | Pi 3B+ | SD card | - | offline | sensor node or donate/recycle | off |
| **pi-zero-*** | Pi Zero 2W (several) | SD card | - | unused | environmental sensors | 24/7 (low power) |
| **charon** | Asus ZenBook Pro UX501vw, 16 GB | NVMe | Intel iGPU + Nvidia 960M | Jellyfin, webtop, idle MC/Terraria | Media engine (QuickSync) | **on-demand** |
| **jade** | Fractal Terra: 9800X3D, 64 GB DDR5, 800 W | NVMe (12 TB planned) | AMD 9070 XT 16 GB | Windows 11 daily driver, AI testing | adult workstation + occasional AI | **on-demand** |
| **methuselah** | Synology 1621+, 32 GB | 32 TB + 1 TB NVMe cache | - | Synology services only | backup vault + media library | 24/7 |

Also present: a Steam Machine (not suitable for this), assorted Arduino Unos, old laptops
(used occasionally to connect), adult cell phones (primary touch points).

## Capacity Headroom (realistic, conservative)

| Device | Headroom | Notes |
|--------|----------|-------|
| sirius (Pi 5, 8 GB) | Moderate | Traefik + Arcane + Filebrowser + n8n is already a lot for 8 GB ARM. Add cautiously; no LLM here. |
| altair (Pi 4, 4 GB) | Moderate | apt-cacher-ng cache must live on external SSD; photo processing is best-effort. |
| vega (Pi 4, 2 GB) | **Tight** | 2 GB total. TURN/STUN is light but it already serves sites. No heavy additions. |
| charon (16 GB, iGPU) | Good while awake | On-demand only; QuickSync for Jellyfin. |
| jade (64 GB, 9070 XT) | High but Windows | Daily driver; AI must not degrade adult work. GPU accel on AMD/Windows is unproven. |
| methuselah (Synology, 32 GB) | High for storage | Restricted Docker; prefer native Synology packages. |

## Hard Constraints & Gotchas

1. **No local LLM on ARM Pis.** Inference belongs on Jade (x86 + GPU) or a future dedicated node.
2. **AMD GPU on Windows**: ROCm/WSL2 support is immature. Treat GPU-accelerated inference as a
   *hypothesis to validate*, not a capability. The AI stretch goal is gated on this.
3. **Jade is a daily driver** for two adults. Anything running there must be scheduled and
   non-disruptive, and must yield instantly.
4. **Charon is a laptop**: heat/noise/power matter. Prefer on-demand; avoid 24/7 heavy transcodes.
5. **vega has 2 GB RAM**: treat as a small, single-purpose node.
6. **Synology Docker is restricted**: prefer native packages; don't assume arbitrary containers.
7. **Mixed architectures** (ARM64 Pi vs x86_64): every service image must have an ARM64 variant
   if it is to run on the Pis. Note this in each compose file.
8. **Firewalla owns DNS/ad-block**: don't duplicate.

## Storage Budget

| Pool | Size | Use | Redundancy |
|------|------|-----|------------|
| Synology volume | 32 TB | photos, media, backups, documents | Synology RAID + snapshots |
| Synology NVMe cache | 1 TB | read cache | - |
| Jade NVMe | 12 TB (planned) | workstation + AI assets | none (workstation) |
| Pi external SSDs | small | OS + service state | none |
| Offsite | Glacier Deep Archive | immutable copy of irreplaceable data | offsite |

**Rule**: irreplaceable data (photos/videos/documents) must exist in at least 3 places:
device of origin, Synology, and offsite (Glacier). Everything else is reproducible.

## Power / Heat / Noise

- Pis and Synology: low, acceptable for 24/7.
- Charon: laptop-class; keep on-demand to avoid heat/noise in shared space.
- Jade: high-power desktop; already on for work, so AI use is "free" only while it is already on.

## Cost Model

- No recurring subscription should exist where a self-hosted equivalent is proven and maintained.
- Electricity is the main marginal cost; on-demand use of already-on machines is preferred.
- Every new service must justify itself against: time saved, money saved, privacy gained, or
  reliability improved. If none, don't deploy it.
