# home

Infrastructure-as-code and documentation for our home lab and home services.

**Core rule**: every service must pay back more time than it costs, or deliver clear privacy,
reliability, or cost value the family can feel. No service exists just because it is interesting.

## Repository layout

```
.
├── README.md                        # this file
├── as-is-analysis.md                # current state (narrative) - keep updated
├── service-catalog.md               # candidate services + value tests
├── to-be-architecture-headless.md   # target architecture (headless-first)
├── docs/
│   ├── implementation-plan.md       # THE phase plan (single source of truth)
│   ├── resources.md                 # hardware capacity + constraints
│   ├── audit.md                     # information/repo/resource audit + fixes
│   ├── migration.md                 # how/why to move to the dedicated repo
│   ├── adr/                         # architecture decision records
│   └── runbooks/                    # add-a-service, verify-backups
├── infra/
│   ├── desired-state/
│   │   └── desired-state.yaml       # what should be running, and where
│   ├── services/                    # one <name>-compose.yml per service
│   └── traefik/                     # proxy config + dynamic (hot-reload) routes
├── playbooks/                       # n8n workflow definitions
├── scripts/                         # helper scripts (sync, verification)
└── templates/                       # reusable templates
```

## How it fits together

```
change in Git  ->  desired-state.yaml  ->  Arcane syncs  ->  Traefik routes  ->  service live
```

- **Git** is the source of truth (ADR 0001).
- **desired-state.yaml** tells Arcane what should be running and on which device.
- **Traefik** hot-reloads routes from `infra/traefik/`.
- **Arcane** is the single management plane and handles updates (ADR 0002) - no Watchtower.

## Start here

1. `docs/implementation-plan.md` - what we are doing and in what order.
2. `docs/resources.md` - what hardware we have and its limits.
3. `service-catalog.md` - what services are worth building.
4. `docs/runbooks/add-a-service.md` - how to add one safely.

## Guiding principles

- Start small, prove value, then expand.
- <5 min/week maintenance per service, averaged.
- One management plane (Arcane), one proxy (Traefik).
- Privacy by default: data stays home unless there is a compelling reason.
- Graceful degradation: if the lab is down, family life continues.
- If a service needs meticulous manual data entry to work, it must save more time than it costs -
  otherwise it is a net negative.

## Status

Foundation phase. See `docs/implementation-plan.md` for the phased roadmap and exit criteria.
