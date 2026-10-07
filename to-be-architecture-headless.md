# Home Lab "To Be" Architecture: Headless-First Design

## Critical Clarification: All Linux Devices Are Headless Servers

All Raspberry Pi devices and Linux systems are headless servers (no monitors/keyboards/touchscreens). They form the "digital spine and nervous system" but are not family touch points.

Only device with regular physical interaction:
- **Fractal Terra PC (Jade)**: Windows 11 PC (adult work/gaming/AI testing)
- **Old laptops**: Used occasionally when needed
- **Cell phones**: Adults' primary mobile devices

## Headless Hardware Role Re-Definition

### **Pi 5 (sirius)** - The Orchestration Brain
- **Role**: Central automation hub (never touched directly)
- **Current**: getArcane, Traefik, Filebrowser (all accessed remotely)
- **To Be**: AI orchestrator (n8n), Local LLM endpoint, Automated backup verification, Network-wide ad/tracking blocker (Pi-hole equivalent)
- **Access**: Only via SSH/Web UI from admin devices (laptops/phones)
- **Family benefit**: More reliable network, fewer ads, automated maintenance

### **Pi 4 4GB (altair)** - The Reliability Worker
- **Role**: Background processing and caching (never touched)
- **Current**: Static site hosting (accessed via browsers on family devices)
- **To Be**: Automated site deployment & verification system, Local caching proxy for updates/speed (apt-cacher-ng equivalent), Family photo processing queue (resize, backup, organize)
- **Access**: Only via monitoring dashboards or API
- **Family benefit**: Faster updates, reliable site availability, organized photos

### **Pi 4 2GB (vega)** - The Connection Enhancer
- **Role**: Communication reliability booster (never touched directly)
- **Current**: Static medical/info sites (accessed via browsers)
- **To Be**: Jitsi Meet TURN/STUN server (improves grandparent video call quality), Automated site health checker & self-healer, Local DNS/cache for faster family site access
- **Access**: Only via monitoring or when troubleshooting
- **Family benefit**: More reliable video calls with grandparents, faster site loading

### **Asus ZenBook Pro (Charon)** - The Media & Creative Engine
- **Role**: Primary transcode/gaming/workstation (already has direct use)
- **Current**: Jellyfin + webtop + occasional gaming
- **To Be**: Hardware-accelerated Jellyfin (Intel QuickSync), Automated media library organization (filebot/sonarr/qbit combo), Weekend creative station (when adults choose to use it)
- **Access**: Direct use when needed (already happens)
- **Family benefit**: Smoother streaming (less buffering), organized media

### **Fractal Terra PC (Jade)** - The AI & Creative Workstation
- **Role**: Adult workstation + occasional AI/family creative time
- **Current**: Windows 11 work/daily driver + AI testing
- **To Be**: Weekend family AI time (scheduled blocks for creative projects), WSL2/Linux VM for server testing (isolated from Windows host), Automated backup verification & restore testing, Local AI model serving (when needed for orchestration)
- **Access**: Direct adult use (unchanged) + scheduled family time
- **Family benefit**: Protected Windows host, scheduled creative AI time

### **Synology 1621+** - The Autonomous Vault
- **Role**: Always-on backup and media server (minimal direct interaction)
- **Current**: Synology services only
- **To Be**: Immutable backup target (Snapshots + S3 Glacier Deep Archive), Media library with automated organization, Surveillance station (only if actual security need demonstrated)
- **Access**: Via Synology UI/apps or automated processes
- **Family benefit**: Photos/videos safe forever, media just works

### **Pi Zero 2W's** - The Environmental Monitors
- **Role**: Pure sensors (zero direct interaction)
- **Current**: Various unused
- **To Be**: Temperature/humidity/air quality sensors in key locations, Door/window status monitors (security + energy efficiency), Leak detectors (under sinks, water heater, etc.)
- **Access**: Only via alerts when something needs attention
- **Family benefit**: Prevents damage, saves on utilities, early warnings

### **Pi 3B+** - Purposeful Retirement or Donation
- **Current**: Offline
- **To Be Options**: Environmental sensor node (if monitoring gaps exist), LoRa emergency comms (only if genuine rural/unstable power concerns), Donate to school/kids' STEM program (teaches next generation), Responsible e-waste recycling (if no clear purpose)
- **NOT**: Kept "just in case" without specific, documented purpose

## Final Architecture Summary (Headless View)

```
                           ┌──────────────────────┐
                           │   Autonomous Vault   │
                           │ (Synology 1621+)     │
                           │ - Encrypted Backups  │
                           │ - Glacier Archive    │
                           │ - Media Library      │
                           │ - Organized Photos   │
                           └─────────┬────────────┘
                                     │
                    ┌────────────────▼────────────┐
                    │       Silent Monitoring     │
                    │ (Alerts only to family chat)│
                    │                             │
    ┌───────────────▼───────────────┐ ┌───────────────▼───────────────┐
    │         Pi 5 (sirius)         │ │    Asus ZenBook (Charon)      │
    │  Headless Orchestrator Brain  │ │  Media Transcoder Engine      │
    │  - n8n workflow automation    │ │  - Hardware-accelerated Jellyfin│
    │  - Network-wide ad/tracking   │ │  - Automated library organizer│
    │    blocker (Pi-hole equiv)    │ │  - Weekend creative station   │
    │  - Local LLM for orchestration│ │                                 │
    │  - Automated backup verifier  │ └────────────…………………………………………………………………………………………………………………………………………………………………………………………………………………………………(truncated)