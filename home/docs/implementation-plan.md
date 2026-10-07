# Home Lab Implementation Plan

> **This is the single source of truth for sequencing.** Other docs describe state, capacity, or
> services; they do not define phases. If something here conflicts with another doc, this wins -
> fix the other doc.

## Phase exit criteria (do not advance until met)

**Phase 0 - Foundation**
- [ ] All service configs live in Git; a change is made by commit only
- [ ] `desired-state.yaml` exists and Arcane (or the sync script) reads it
- [ ] A Git push reaches the deploy step (webhook or manual trigger) and a route goes live
- [ ] Observability: a dashboard shows uptime/health for all running services
- [ ] Alerting: failures reach the family chat
- [ ] Backup verification runbook executed at least once end-to-end (sample restore succeeds)
- [ ] Maintenance time measured and under budget for 2 consecutive weeks

**Phase 1 - Core family protection**
- [ ] Photo/video backup verified: 3-2-1 rule holds, weekly sample restore passes
- [ ] Jellyfin HW-accelerated; media findable in seconds
- [ ] Calls with grandparents measurably more reliable
- [ ] At least one environmental sensor alerting correctly (and not crying wolf)

**Phase 2 - Time & money savings**
- [ ] At least one recurring subscription cancelled with no loss of function
- [ ] Patching automated with working rollback
- [ ] Media management needs <5 min/month

**Phase 3 - Growth & enhancement**
- [ ] Each new service passes the Invisible Value Test and the family check-in
- [ ] AI stretch items only attempted if their gate (GPU validation) is passed

Phased approach: improvements to existing services, new services, and long-term stretch goals. Core principle: every change must pay back more time than it costs, or deliver privacy/reliability/cost value family can feel.


Phased approach: improvements to existing services, new services, and long-term stretch goals. Core principle: every change must pay back more time than it costs, or deliver privacy/reliability/cost value family can feel.

## 1. Improvements to Existing Services

### getArcane (Orchestration Dashboard)
- Health/resource/backup-status dashboard tiles
- Automated healing rules (restart failed containers on healthcheck failure)
- Audit log of all actions
- Keep as single management plane (avoid duplicate tooling)

### Traefik (Reverse Proxy)
- Automated TLS via Let's Encrypt DNS-01 (only if we expose anything)
- Security headers + compression middleware stack
- Access logs to a simple analytics view (GoAccess) for debugging only
- Rate limiting on any externally exposed route

### Jellyfin (Media Server)
- Tune Intel QuickSync for H.264/H.265/VP9 (better playback, less CPU heat)
- Scheduled metadata refresh
- Per-device transcoding profiles (phone/tablet/TV) to save bandwidth
- Verify current library mount from Synology is optimal

### Dozzle (Container Monitoring)
- Auth via Authelia (currently likely open on LAN)
- Persistent log storage across restarts
- Alert webhook on crash/restart loops

### Synology (Storage)
- Snapshot schedule + retention policy review
- Immutable backup target verification (this is the anchor of the whole system)

## 2. New Services by Phase

### Phase 0: Foundation (Month 0-1) - ENABLE EVERYTHING ELSE
- GitOps: move service configs into Git, webhook auto-deploy, Arcane desired-state sync
- Observability: uptime + health dashboard (Uptime Kuma or Prometheus/Grafana-lite)
- Backup verification: daily Synology snapshot check, weekly test-restore, monthly Glacier check
- Service templates: base compose templates (web app, worker, sensor) - already started
- Network ad-block: Firewalla already does ads; add local caching proxy (apt-cacher-ng) on altair to cut update bandwidth/time. Pi-hole only if Firewalla proves insufficient - avoid duplicate.

### Phase 1: Core Family Protection (Months 2-4) - HIGH TRUST VALUE
- Immutable Memory Vault: phone -> Synology -> encrypted -> Glacier Deep Archive; monthly verify
- Media Library Autonomy: Jellyfin HW accel + auto-organize; "find any show fast"
- Communication Reliability: Jitsi/TURN on vega for reliable grandparent calls
- Environmental Early Warning: Pi Zero W temp/humidity/leak sensors, alert-only to family chat

### Phase 2: Time & Money Savings (Months 5-8)
- Subscription eliminator: replace photo storage, movie rentals, music with self-hosted + legal
- Household ops automation: patching w/ rollback, log aggregation, anomaly alerts
- Intelligent media management: dup detection, format conversion, cleanup

### Phase 3: Growth & Enhancement (Months 9-18)
- Learning/development helper: curated, ad-free edu content for kids on old tablets
- Creative family time: weekend AI-assisted projects (only when hardware supports)
- Proactive home management: predictive alerts from sensor trends

## 3. Stretch Goals (Multi-Year) + Seeding Strategies

Rule: never build the stretch goal directly. Seed it with small changes that are useful on their own TODAY.

### 3.1 AI Lab Orchestrator (the "one prompt -> service" vision)
- NOW seed: structured logging + metrics from all services (Phase 0)
- NOW seed: machine-readable service metadata (extend service-catalog.md into JSON/YAML)
- NOW seed: n8n deploy workflow + service templates (done, mockups)
- LATER: local LLM on Jade/Fractal Terra (GPU) for NL -> compose generation
- LATER: feedback loop - services report usage/perf to improve future deploys
- GATE: only when GPU idle capacity is real and reliable, not aspirational

### 3.2 Full Home Automation
- NOW seed: MQTT as common protocol; environmental sensors (Phase 1)
- LATER: integrate smart plugs/lights/thermostat via Home Assistant (only if a real pain point)
- WARNING: highest "glues family to app" risk - require a frictionless win before expanding

### 3.3 Personal Data Vault + Private Assistant
- NOW seed: local search index over family docs/photos (MeiliSearch/Typesense)
- NOW seed: family knowledge base in Markdown (this home/ dir is the start)
- LATER: local LLM Q&A over indexed data; simple web/voice interface

### 3.4 Advanced Media Experience
- NOW seed: Jellyfin metadata optimization (Phase 1) + auto-organization (Phase 2)
- LATER: recommendations, cross-device sync playback

### 3.5 Health & Wellness Monitoring
- NOW seed: environmental air quality/temp/humidity sensors (Phase 1)
- LATER: import wearable data, correlate with environment (allergy/asthma triggers)
- CAUTION: only pursue if it genuinely reduces worry/time, not adds tracking burden

## 4. Guardrails (from hard-won experience)
- If a service requires meticulous manual data entry (e.g. grocery inventory), it must SAVE more time than it costs or it's a net negative.
- <5 min/week maintenance per service, averaged.
- One management plane (Arcane). One proxy (Traefik). Don't add K8s/Pi-hole/etc. without removing something.
- Every service must be explainable to family in one sentence of concrete benefit.
- Graceful degradation: homelab down != family life disrupted.

## 5. Immediate Next Steps
1. Push foundation files to the dedicated `home` repo (blocked: repo not accessible yet)
2. Set up Git-push -> Traefik/Arcane sync webhook
3. Deploy first Tier 1 service: Immutable Memory Vault
4. Family feedback after 2 weeks; keep/fix/kill
