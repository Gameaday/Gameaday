# Services

One file per service: `<service-name>-compose.yml`. Each file is the deployable unit for that
service and is referenced by `../desired-state/desired-state.yaml`.

## Naming

- Lowercase, hyphenated: `immutable-memory-vault-compose.yml`
- The name must match the `name` field in `desired-state.yaml`.

## Conventions

- Pin image versions (`image: foo:1.2.3`), never `latest`.
- `restart: unless-stopped` on every long-running service.
- Note the **target device** and **ARM64 support** in a comment at the top.
- No secrets inline; reference a secret file or environment injection (see `.gitignore`).
- Keep services small and single-purpose.

## Current services

| File | Purpose | Target device | ARM64 |
|------|---------|---------------|-------|
| `immutable-memory-vault-compose.yml` | Photo/video backup verification to Synology + Glacier | methuselah | n/a |
| `network-cache-compose.yml` | Local package cache (apt-cacher-ng). No Pi-hole - Firewalla owns DNS (ADR 0004) | altair | yes |
| `communication-reliability-booster-compose.yml` | Jitsi TURN/STUN for reliable calls | vega | yes |
| `environmental-early-warning-compose.yml` | Temp/humidity/leak sensor node | pi-zero-* | yes |
| `traefik-compose.yml` | Reverse proxy (hot-reload) | sirius | yes |
| `getarcane-compose.yml` | Management plane | sirius | yes |
| `jellyfin-compose.yml` | Media server (QuickSync) | charon | no (x86) |
| `dozzle-compose.yml` | Container log viewer | all | yes |
| `shape-learning-game-compose.yml` | Example/stretch: generated mini-game | (stretch) | yes |
| `todo-dashboard-compose.yml` | Example/stretch: generated dashboard | (stretch) | yes |

## Adding a service

Follow `../../docs/runbooks/add-a-service.md`.
