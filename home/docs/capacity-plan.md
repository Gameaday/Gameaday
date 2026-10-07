# Balanced Service Load (Capacity Plan)

How much extra we can run on each device **without hurting core services**.

## The core principle: no bad neighbors

Memory is the binding constraint, not CPU (confirmed by the charon observation). Two rules make
"more juice" safe:

1. **Reserve headroom.** Never plan to use 100% of RAM. Leave ~30% for the OS, page cache, and
   spikes. A Pi that swaps is a Pi that feels broken.
2. **Hard-limit every container.** Set a memory limit per container so one runaway service cannot
   starve the others. This is the single most important habit for a mixed-use node.

CPU is usually fine on idle-ish home services; it only matters for transcoding, OCR, builds, and
model inference. Those are the "heavy" class and get scheduled/limited explicitly.

## Service size classes (rough RAM)

| Class | RAM | Examples |
|-------|-----|----------|
| tiny | < 50 MB | mosquitto, ntfy, coturn, node_exporter, dozzle |
| light | 50-200 MB | Uptime Kuma, Vaultwarden, Actual Budget, Gitea/Forgejo |
| moderate | 200-600 MB | n8n, Home Assistant, *arr apps (each), Mealie |
| heavy | > 600 MB | Jellyfin transcode, webtop, Paperless (OCR), game servers, LLMs |

## Per-device budget

| Device | RAM | Baseline (OS + current) | Usable for services | Target service load |
|--------|-----|-------------------------|---------------------|---------------------|
| sirius (Pi 5) | 8 GB | ~1.5 GB (Arcane, Traefik, Filebrowser, Dozzle) | ~5 GB | 4-6 light/moderate |
| altair (Pi 4) | 4 GB | ~0.5 GB (static site) | ~2.5 GB | 2-3 light/tiny |
| vega (Pi 4) | 2 GB | ~0.5 GB (sites) | ~1 GB | 1-2 tiny only |
| pi-zero-* | 512 MB | ~100 MB | ~300 MB | 1 tiny each (sensor) |
| pi3b | 1 GB, SD | - | - | 0 (retire/donate) |
| charon | 16 GB | ~2 GB OS + Jellyfin + *arr | ~10 GB | 3-5 light + 1 heavy on-demand |
| methuselah (Synology) | 32 GB | Synology services | - | native packages only |
| jade | 64 GB | workstation | - | 0 network services |

## Rules of thumb

- **Pi Zero 2W**: exactly one tiny service (it is a sensor, not a host).
- **Pi 3B+**: not a service host. Retire, donate, or use as a spare sensor with a real disk.
- **Pi 4 2 GB (vega)**: 1-2 tiny services max. No Node/Python/Java apps. Watch RAM constantly.
- **Pi 4 4 GB (altair)**: 2-3 light services, or 1 moderate + 1 tiny.
- **Pi 5 8 GB (sirius)**: 4-6 light/moderate. This is the orchestration brain - protect it.
- **charon 16 GB**: several light always-on + exactly one heavy at a time.
- **Synology**: native packages only; its Docker is restricted and it is the storage spine.
- **jade**: none. Workstation only.

## Device-by-device brainstorm

### sirius (Pi 5, 8 GB) - the orchestration brain
**Keep core**: Arcane, Traefik, Filebrowser.
**Good fits (add here first)**:
- `n8n` (moderate) - automation backbone; needed for GitOps/self-audit.
- `Uptime Kuma` (light) - observability + alerting to family chat.
- `ntfy` (tiny) - self-hosted push notifications for alerts. Pairs with Uptime Kuma.
- `mosquitto` (tiny) - MQTT broker for sensors and future automation.
- `dozzle` (tiny) - already present.
**Stretch**: a light dashboard (Homepage/Homarr, light) as a single pane of glass.
**Red lines**: no LLM, no Grafana+Prometheus full stack, no database-heavy apps. This box must
stay responsive because everything else depends on it.

### altair (Pi 4, 4 GB) - the reliability worker
**Keep core**: historylabs.dev static site.
**Good fits**:
- `apt-cacher-ng` (tiny RAM, disk-heavy) - local package cache; saves bandwidth/time.
- `Vaultwarden` (tiny) - password manager; replaces a paid subscription, big privacy win.
- `Actual Budget` (light) - self-hosted budgeting; replaces YNAB-style subscriptions.
- `Gitea/Forgejo` (light) - optional self-hosted Git if we ever want off-GitHub (not required).
**Stretch**: `Mealie` (moderate) - recipes; only if the family will actually use it.
**Red lines**: one moderate max; no OCR/ML workloads; keep disk churn on the external SSD.

### vega (Pi 4, 2 GB) - the connection enhancer
**Keep core**: the medical sites.
**Good fits**:
- `coturn` (tiny) - TURN/STUN for reliable grandparent calls.
- `node_exporter` (tiny) - so monitoring can see this host.
**Stretch**: none meaningful.
**Red lines**: nothing Node/Python/Java; 2 GB total is genuinely tight. If we need more here, the
right move is to consolidate vega's sites onto another host and free the device.

### pi-zero-* (512 MB) - sensors
**Fits**: exactly one tiny service each - a sensor reporter (temp/humidity/leak) publishing to
mosquitto. Possibly a small camera node later.
**Red lines**: anything interactive or memory-hungry.

### pi3b (1 GB, SD card) - retire
**Reality**: too weak, SD card unreliable for 24/7 writes.
**Options**: donate to a STEM program, or use as a one-off sensor with a real disk. Not a host.

### charon (16 GB, iGPU) - the shared workhorse
**Keep core**: Jellyfin (QuickSync).
**Good fits (always-on, idle-light)**:
- `*arr` stack: Prowlarr + Sonarr + Radarr (+ qBittorrent) - each light, bursts on import.
- `Home Assistant` (moderate) - if/when we want real automation; it is the natural hub.
- `Paperless-ngx` (moderate, OCR bursts) - the family document vault; high time-saving value.
**On-demand (one heavy at a time)**:
- `webtop` during the work day (shares the iGPU).
- A game server, started on demand only - never resident.
**Red lines**: no Immich (that is a 2030 NAS job), no always-on transcoding loops, no second
resident heavy service. Enforce memory limits so webtop cannot starve Jellyfin.

### methuselah (Synology, 32 GB) - the vault
**Fits**: native packages only - Synology Photos, Drive, Hyper Backup, snapshot replication.
**Red lines**: no custom containers (restricted Docker), no VMs, no databases for other apps.
It is the storage spine; treat it as boring infrastructure.

### jade (Fractal Terra) - workstation
**Fits**: nothing on the network. It is a daily driver for two adults.
**Red lines**: no network services, no resident AI. (ADR 0003.)


## Recommended placement (candidate services -> best host)

| Service | Class | Best host | Why | Value |
|---------|-------|-----------|-----|-------|
| Uptime Kuma | light | sirius | needs to be up when others are down | observability |
| ntfy | tiny | sirius | alert delivery | observability |
| mosquitto | tiny | sirius | shared sensor bus | enables automation |
| n8n | moderate | sirius | orchestration | automation backbone |
| apt-cacher-ng | tiny | altair | disk cache, low RAM | bandwidth/time |
| Vaultwarden | tiny | altair | low risk, high privacy | privacy/cost |
| Actual Budget | light | altair | low RAM | cost/time |
| coturn (TURN/STUN) | tiny | vega | already serves that network | reliability |
| Jellyfin | heavy | charon | QuickSync | media |
| *arr stack | light each | charon | idle-light, bursty | media automation |
| Paperless-ngx | moderate | charon | OCR bursts, document vault | time/privacy |
| Home Assistant | moderate | charon | automation hub | convenience |
| Synology Photos/Drive/Backup | native | methuselah | native only | privacy/reliability |
| sensor reporters | tiny | pi-zero-* | one per sensor | safety |

**Deliberately NOT placed anywhere**: Pi-hole/AdGuard (Firewalla owns it), Immich (2030 NAS),
Nextcloud (Synology Drive covers it), any LLM (deferred), Kubernetes (never).

## charon arbitration model (the one-heavy-at-a-time rule)

1. **Memory limits per container** so webtop cannot starve Jellyfin (and vice versa).
2. **Schedule**: webtop is a work-hours service; Jellyfin is an evening service. Their iGPU use
   rarely overlaps in practice, but the limits make overlap survivable.
3. **On-demand only** for game servers and anything else heavy: start/stop, never resident.
4. **Watch the swap**, not just CPU. If charon swaps, it is over budget regardless of load average.

## Ramp plan (add in waves, measure between)

- **Wave 0 (Phase 0)**: sirius: Uptime Kuma + ntfy (+ n8n if not already). Measure 1 week.
- **Wave 1 (Phase 1)**: vega: coturn; altair: apt-cacher-ng; charon: Jellyfin tuning.
- **Wave 2 (Phase 2)**: altair: Vaultwarden + Actual Budget; charon: *arr stack.
- **Wave 3 (Phase 3)**: charon: Paperless-ngx / Home Assistant (choose based on real need).
- **Wave 4 (stretch)**: AI on charon/Pi (tiny models only); NAS migration ~2030.

Rule: **add one service, measure for a week, then add the next.** Never batch-add.

## Over-budget signals (back off if you see these)

- Swap in use, or OOM kills in `dmesg` / container logs.
- Load average persistently above ~75% of core count.
- Services feel sluggish; Jellyfin buffers; the web UI lags.
- Fan noise / heat rising (charon only really when the Nvidia GPU is used).
- Maintenance time creeping past the <5 min/week budget.

## How to measure (cheap, no heavy stack)

- `docker stats` per host for live memory/CPU per container.
- `node_exporter` on each host + a light monitor (Uptime Kuma for up/down; Beszel for resources).
- Dozzle for logs; Synology for disk health.
- A simple weekly 2-minute glance, not a dashboard you must study.

## Guardrails recap

- Reserve ~30% RAM headroom per host.
- Hard memory limits on every container.
- One heavy service at a time on charon.
- Add one service at a time; measure before the next.
- If a service's value is unclear or it needs manual data entry, it does not ship.

