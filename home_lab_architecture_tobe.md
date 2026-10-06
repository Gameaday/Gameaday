# Home Lab Architecture – To‑Be (Future State)

## Vision
A family‑centric, low‑maintenance home lab that provides reliable, useful services (media, learning, chores, communication) while offering a safe sandbox for experimenting with AI and other cutting‑edge technologies. The control plane runs entirely on low‑power hardware (Raspberry Pi 5/4) and only leverages the Windows PC for occasional GPU‑heavy workloads (e.g., Ollama LLM inference) when explicitly requested, keeping noise and heat to a minimum.
## Guiding Principles (unchanged from As‑Is)
- **Family‑First Quality** – Services must solve a real need or bring joy.
- **Reliability > Novelty** – Prefer proven stacks; add complexity only when it yields measurable gain.
- **Low‑Maintenance / Self‑Healing** – Automated health‑checks, auto‑restart, easy rollback via Git.
- **Power‑Efficiency** – Control plane ≤ 10 W idle; GPU workloads only on demand.
- **Git‑Ops / Versioned Everything** – All infrastructure as code; changes via PR/QA.
- **Observable & Auditable** – Logs (Dozzle), metrics (Prometheus/Grafana), alerts (Gotify/Telegram).
- **Extensible but Not Over‑Engineered** – New service = new folder under `services/`.
- **Security‑by‑Default** – Docker networks, read‑only fs where possible, Traefik middleware (auth, rate‑limit).
- **Future‑Proof for AI** – GPU agent available via WSL2 on Windows PC, but only invoked when a service labels `requires-gpu: true`.
## High‑Level Layout (text diagram)

```
                                 ┌─────────────────────────────┐
                                 │  Internet (Optional WAN)    │
                                 │  – Dynamic DNS → Traefik    │
                                 └─────────────┬───────────────┘
                                               ▼
                                      ┌─────────────────┐
                                      │  Traefik (Pi 5)  │
                                      │  - HTTP/S router │
                                      │  - Middleware   │
                                      │  - TLS (LE)     │
                                      └───────┬─────────┘
                                              │
          ┌─────────────────────┬─────────────┼─────────────────────┐
          │                     │             │                     │
          ▼                     ▼             ▼                     ▼
  ┌─────────────────┐   ┌──────────────┐  ┌───────────────┐   ┌─────────────┐
  │  Service A      │   │  Service B   │  │  Service C    │   │  Service D  │
  │  (docker‑compose│   │  (docker‑compose│ │  (docker‑compose│ │  (docker‑compose│
  │   + traefik‑    │   │   + traefik‑   │ │   + traefik‑   │ │   + traefik‑   │
  │   route.yaml)   │   │   route.yaml)  │ │   route.yaml)  │ │   route.yaml)  │
  └───────┬─────────┘   └───────┬────────┘  └───────┬─────────┘   └───────┬───────┘
          │                     │             │                     │
          │                     │             │                     │
          ▼                     ▼             ▼                     ▼
   ┌─────────────────┐  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
   │  Arcane Agent   │  │  Arcane Agent   │ │  Arcane Agent   │ │  Arcane Agent   │
   │  (Pi 5)         │  │  (Pi 4 Altair)  │ │  (Pi 4 Vega)    │ │  (Charon/WSL2) │
   └───────┬─────────┘  └───────┬─────────┘ └───────┬─────────┘ └───────┬─────────┘
          │                     │             │                     │
          └───────┬─────────────┼─────────────┼─────────────────────┘
                  ▼             ▼             ▼
           ┌─────────────────────────────┐
           │   Control Plane (Pi 5)      │
           │  - Filebrowser (config edit)│
           │  - Dozzle (logs)            │
           │  - Prometheus + Grafana    │
           │  - Gotify (alerts)          │
           │  - UPS + nut (power safety)│
           │  - (Optional) Gitea + Woodpecker CI (self‑hosted)│
           └─────────────────────────────┘
```
## Concrete Component Choices

| Layer | Tool | Why it fits |
|-------|------|-------------|
| **Ingress / Reverse Proxy** | **Traefik v2** (already) | Dynamic routers, Let’s Encrypt, middleware, hot‑reload via watched directory. |
| **Fleet Orchestration** | **Arcane** (already) | Simple API to deploy/update Docker‑Compose stacks, lightweight agent on each node. |
| **Control‑Plane Host** | **Pi 5 (8 GB)** + optional second Pi 4 for HA | ~5 W idle, enough for Filebrowser, Dozzle, Prometheus, Grafana, Gotify. |
| **Log Viewer** | **Dozzle** (already) | Real‑time container logs, zero config overhead. |
| **Metrics + Visualization** | **Prometheus + Grafana** (Docker) | Scrape Arcane `/metrics` and cAdvisor; alert on high CPU/Memory or crash loops. |
| **Alerting** | **Gotify** (self‑hosted) or **Telegram bot** | Low‑overhead push notifications for build/deploy/rollback events. |
| **Config Editing (occasional)** | **Filebrowser** (already) | Web‑based file edit for Traefik hot‑folder or occasional compose tweaks. |
| **Git Hosting (short‑term)** | **GitHub private repo** | Zero‑maintenance, reliable CI via GitHub Actions. |
| **Git Hosting (long‑term local option)** | **Gitea** + **Woodpecker** CI (Docker on Pi 5) | Self‑hosted, full data sovereignty; can replace GitHub later. |
| **CI / QA** | **GitHub Actions** (now) → optionally **Woodpecker** (self‑hosted) | Runs on Microsoft runners (no extra HW) or self‑hosted runner on Pi 5 if desired. |
| **Testing / Health‑Checks** | **Playwright** (containerized) + `docker compose config` + simple `curl` | Guarantees YAML validity, service starts, and basic HTTP/200 works before Traefik exposure. |
| **Secrets Management** | **Docker secrets** (via `docker compose`) or **HashiCorp Vault** (if needed) | Keeps API keys, model tokens out of compose files. |
| **Backup / Snapshots** | **Synology Snapshots** (Pi 5 SSD) + **Restic** to back up Git repo & volumes to Synology | Quick restore of control‑plane; Git repo is ultimate source of truth. |
| **Power Management** | **UPS** (USB‑connected to Pi 5) + **nut** daemon | Graceful shutdown on power loss; prevents SD‑card corruption. |
| **Network Segmentation** | **VLANs** on router (IoT, Guest, Lab) + optional **Pi‑hole** for ad‑blocking | Isolates lab traffic, reduces attack surface. |
| **AI Inference (GPU)** | **Ollama** (LLM) + **Whisper.cpp** (STT) + **Bark/Kokoro** (TTS) + **ComfyUI** (Stable Diffusion) – all as Docker containers with GPU access via `device-requests` in compose. | Runs **only** on the Windows PC agent (via WSL2) when a service labels `requires-gpu: true`. Otherwise the PC can sleep/suspend, saving power and noise. |
| **Model Registry (optional)** | Local `models/` folder in Git repo, versioned with Git‑LFS | Pin exact model weights used by a service for reproducibility. |
| **DNS (local)** | **Pi‑hole** or **CoreDNS** on Pi 5 | Provides `*.home.lab` domain for services; can forward upstream. |
## Decision Framework for New Services (Checklist)

Before writing any compose file, answer **YES** to the majority:

- [ ] Solves a real, recurring pain point for a family member?  
- [ ] Can be expressed as a Docker‑Compose stack (stateless or easily stateful)?  
- [ ] Has a clear health‑check endpoint or observable output (HTTP 200, logs, metrics) that Playwright/Prometheus can verify?  
- [ ] All configuration (including AI model prompts) can be versioned in Git?  
- [ ] If GPU/ML needed, can be constrained to run only on the custom PC agent?  
- [ ] Resource footprint fits the host it will run on?  
- [ ] Has a sensible default (e.g., off‑by‑middle‑night) that reduces power when idle?  
- [ ] Intent documented in a `README.md` inside the service folder?  
- [ ] Plan for de‑commissioning if no longer used?  

If more than two answers are **NO**, place the idea in `docs/ideas.md` and revisit later.
## Phased Roll‑out Plan

| Phase | Goal | Concrete Actions | Success Metric |
|-------|------|------------------|----------------|
| **0 – Foundation** | Reliable, observable control plane. | - Verify Traefik hot‑folder works.<br>- Confirm Arcane can deploy a simple `whoami` stack to each agent.<br>- Install Dozzle on each node.<br>- Add Prometheus + Grafana (scrape Arcane & cAdvisor).<br>- Set up UPS + nut daemon on Pi 5. | All nodes appear in Grafana; you can deploy/remove a test stack via Arcane API with no manual SSH. |
| **1 – GitOps Pipeline** | All infrastructure changes flow through Git + automated QA. | - Create `ai-lab-services` repo (already done).<br>- Add a `hello-world` service (nginx + Traefik route).<br>- Write `.github/workflows/qa-deploy.yml` (QA with `docker compose config` + Playwright health check).<br>- Test PR → QA passes → merge → Deploy job writes Traefik route & calls Arcane API.<br>- Verify service appears at `hello.home.lab` instantly. | PR merges trigger automatic deploy; rolling back via `git revert` redeploys previous version. |
| **2 – Observability & Alerting** | Know when something is wrong before users notice. | - Add Prometheus alerts for: container restart loops, CPU > 80% for 5 min, missing Traefik route.<br>- Route alerts to Gotify/Telegram.<br>- Enable Dozzle dark mode & occasional log‑review habit. | You receive an alert when you intentionally break a service (e.g., bad compose). |
| **3 – First AI‑Powered Service (GPU on demand)** | Test the GPU agent path with a simple, fun AI demo. | - Create `services/ai-storyteller/` with: <br>  • `docker-compose.yml` that runs Ollama (LLM) + a tiny Flask API that returns a story based on a prompt.<br>  • `traefik-route.yaml` for `story.home.lab`.<br>  • Label `requires-gpu: true`.<br>  • QA job includes a `curl` to the API and checks for non‑empty JSON.<br>  • Deploy to custom PC agent (WSL2). | Story endpoint returns a coherent paragraph; GPU utilization spikes on PC during request, then drops. |
| **4 – Family‑Oriented Service** | Deploy something that adds daily value. | - Example: **Chore Board** (simple Kanban using `wekan` or custom React+Node).<br>  • Stores data in a PostgreSQL volume (backed up via Snapshots).<br>  • Access via `chores.home.lab` with basic HTTP auth (Traefik middleware).<br>  • QA: login flow test with Playwright.<br>  • Deploy to Pi 4 Altair (low‑power, always on). | Family can add/check off chores from phones/tablets; board persists across reboots. |
| **5 – Backup & Disaster Recovery Drill** | Prove you can recover from a total control‑plane loss. | - Snapshot Pi 5 SSD via Synology.<br>- Simulate a corrupt SD card: reflash a fresh OS, reinstall Docker + Arcane agent only.<br>- Restore the `ai-lab-services` repo (Git clone).<br>- Run a one‑time “redeploy all” script that reads the repo and calls Arcane API for each service folder.<br>- Verify all services come back. | All services return to last known‑good state within ~10 min of bare‑metal recovery. |
| **6 – Optional: Local Git & CI** | If you decide to go fully self‑hosted. | - Deploy Gitea + Woodpecker on Pi 5.<br>- Migrate the repo.<br>- Decommission GitHub Actions (keep as mirror for off‑site backup). | All push/pull and CI runs happen on local hardware; external dependency reduced to internet for model pulls only. |
| **7 – Continuous Improvement Loop** | Keep the lab fresh without churn. | - Monthly “lab retrospective”: review Grafana panels, alert logs, usage stats.<br>- Archive unused services to `docs/archived/`.<br>- Retire hardware that consistently shows > 90% idle for 3 months (repurpose or donate). | Lab stays lean, purposeful, and cost‑effective over years. |
## Sample Files to Get You Started

### `services/hello-world/docker-compose.yml`
```yaml
version: "3.8"
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"   # only used for health check; Traefik routes to container port 80
    volumes:
      - ./html:/usr/share/nginx/html:ro
```
Create a simple `services/hello-world/html/index.html` with “Hello, Lab!”.

### `services/hello-world/traefik-route.yaml`
```yaml
http:
  routers:
    hello-world:
      rule: "Host(`hello.home.lab`)"
      service: hello-world
      entryPoints:
        - web
  services:
    hello-world:
      loadBalancer:
        servers:
          - url: "http://<TARGET-AGENT-IP>:8080"
```
Replace `<TARGET-AGENT-IP>` with the IP of the agent you want to run on (later you can let Arcane fill this via labels or a templating step).

### `.github/workflows/qa-deploy.yml` (starter)
```yaml
name: QA & Deploy

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  qa:
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request'
    steps:
      - uses: actions/checkout@v4
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Validate compose files
        run: |
          find services -name "docker-compose.yml" -exec docker compose -f {} config \; || exit 1
      - name: Run Playwright health check (if service exposes a port)
        env:
          PLAYWRIGHT_BROWSERS_PATH: 0
        run: |
          npm init -y
          npm i -D @playwright/test
          npx playwright install --with-deps
          find services -name "docker-compose.yml" -exec sh -c '
            file="{}";
            dir=$(dirname "$file");
            port=$(grep -oP '"\K\d+(?=:)" "$file" | head -n1);
            if [ -n "$port" ]; then
              echo "Testing $dir on port $port";
              docker compose -f "$file" up -d;
              sleep 5;
              npx playwright test --timeout 10s \
                -c "{\"use\":{\"baseURL\":\"http://localhost:$port\"}}" \
                || { docker compose -f "$file" down; exit 1; };
              docker compose -f "$file" down;
            fi' \;
      - name: Post QA result
        if: failure()
        run: |
          echo "::error::QA failed for one or more services"

  deploy:
    needs: qa
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    steps:
      - uses: actions/checkout@v4
      - name: Set up Arcane CLI (if available) or use curl
        run: |
          # If you have the arcane binary:
          # curl -L https://get.arcane.io/arcane -o arcane && chmod +x arcane && sudo mv arcane /usr/local/bin/
          # For now, use curl to the API:
          echo "Using curl to call Arcane API"
      - name: Deploy each service
        env:
          ARCANE_ENDPOINT: http://<PI5_IP>:3552   # set via repo secret
        run: |
          find services -mindepth 2 -maxdepth 2 -type d | while read svc; do
            name=$(basename "$svc")
            echo "Deploying $name"
            payload=$(cat <<EOF
            {
              "name": "$name",
              "compose": $(docker compose -f "$svc/docker-compose.yml" config --json | jq -c .),
              "traefikRoute": $(cat "$svc/traefik-route.yaml" | jq -Rs .)
            }
EOF
            )
            curl -s -X POST "$ARCANE_ENDPOINT/api/stacks" \
              -H "Content-Type: application/json" \
              -d "$payload"
          done
      - name: Tag deploy
        run: |
          TAG="deploy/$(date +%Y%m%d-%H%M%S)-${GITHUB_SHA::8}"
          git tag "$TAG"
          git push origin "$TAG"
```
> **Tip:** Replace `<PI5_IP>` with your Pi 5’s address (or store it as a repository secret). The workflow above is a minimal starting point; you can later enrich it with:
> - Selecting the target agent based on labels (`requires-gpu: true` → custom PC agent).  
> - Posting results to Gotify/Telegram.  
> - Running a second Playwright test *after* deploy to verify the live endpoint.
## Keeping the Lab Lean Over Time

1. **Monthly “Service Health” Review**  
   - Grafana dashboard: show `up{job="arcane"}` for each stack, average CPU/Memory, restart count.  
   - Anything with **restart > 5** in the month or **CPU avg > 70%** gets a ticket to investigate or retire.

2. **Quarterly “Power Audit”**  
   - Use a plug‑in power meter (or the UPS’s readings) to log idle watts of each node.  
   - If a node idles > 15 W for > 2 weeks, consider consolidating its workloads or repurposing it.

3. **Bi‑annual “Git‑Housekeeping”**  
   - Archive service folders that haven’t had a commit in 6 months (`mv services/old-service docs/archived/`).  
   - Squash old tags if the repo gets heavy (`git tag -l | xargs git tag -d` then push new tags only for recent releases).

4. **Yearly “Hardware Refresh”**  
   - Evaluate whether a newer SBC (e.g., Orange Pi 5, Rock 5B) offers better perf/watt for the control plane.  
   - Keep the Synology as your bulk storage & backup target—its reliability is already proven.

## Quick Reference Cheat‑Sheet (copy into your README)

```
# Home Lab – Guiding Checklist

[ ] Family‑first: Who benefits daily?
[ ] Reliable: Does it have a health‑check & auto‑restart?
[ ] Low‑maintenance: Can I fix it with `git revert`?
[ ] Power‑efficient: Does it run on Pi unless GPU needed?
[ ] Git‑ops: All configs in repo, PR/QA required.
[ ] Observable: Logs (Dozzle), metrics (Prometheus/Grafana), alerts (Gotify).
[ ] Secure: Traefik middleware (auth, rate‑limit), read‑only fs where possible.
[ ] Extensible: New service = new folder under services/
[ ] AI‑ready: Label `requires-gpu: true` → custom PC agent (WSL2).
```

Tick the boxes before you open a PR. If any box stays unchecked, iterate the idea until it does.