# Home Lab As-Is Analysis & Direction

## Current Infrastructure Overview

### Network Infrastructure
- **Internet**: Fios fiber optic connection
- **Security/DHCP/Ad-blocking**: Firewalla Purple (provides firewall, DHCP, VPN, and built-in ad-blocking)
- **Wired Network**: 2.5GB Ethernet backbone
- **Wireless**: ASUS XD5 access points (WiFi 6)
- **Future Plans**: Replace Firewalla with higher-speed solution without external cloud management; upgrade to WiFi 7 APs when affordable

### Hardware Distribution
- **Pi 5 (sirius)** - 8GB, external SSD: Runs main getArcane instance, Traefik, Filebrowser
- **Pi 4 4GB (altair)** - External SSD: Hosts historylabs.dev zensical site
- **Pi 4 2GB (vega)** - External SSD: Hosts atlasofpelvicsurgery.org sites
- **Pi 3B+** - Offline (replaced by vega)
- **Pi Zero 2W**s: Various unused
- **Asus ZenBook Pro UX501vw (Charon)** - Nvidia 960M, 16GB RAM, NVME: Jellyfin + webtop (Jellyfin already deployed in stack)
- **Fractal Terra PC (Jade)** - 9800X3D, 64GB DDR5, 9070Xt 16GB GPU, 800W PSU, Windows 11: AI testing node, daily driver for adults
- **Synology 1621+** - 32GB RAM, 32TB storage + 1TB NVME cache: Central storage

### Software Stack
- **getArcane**: Unified management dashboard across all Linux devices (also manages container updates, making watchtower unnecessary)
- **Traefik**: Reverse proxy with hot-directory configuration (watches for YAML changes)
- **Docker**: On all Linux devices (Synology has restricted Docker)
- **Each Pi**: dozzle (container viewer) + Arcane agent
- **Jellyfin**: Already deployed on network (media server)
- **Git**: Central repo at GitHub.com/Gameaday/Gameaday (currently used for profile/README site)

### Key Observations
1. **GitHub already used**: Repo exists for profile site, but not yet leveraged for infrastructure-as-code
2. **Traefik hot-reloading**: Available - writes YAML files to watched directory for automatic reload
3. **Arcane API**: Running on Sirius (Pi 5) at port 8080 (internal), manages container updates across fleet
4. **Jellyfin already present**: Media server is already in operation
5. **Ad-blocking handled**: Firewalla Purple provides network-level ad blocking (reduces immediate need for Pi-hole)
6. **Update management**: Arcane handles container updates, making solutions like watchtower redundant
7. **AI/ML gap**: No local LLM/Stable Diffusion installed on any device in this session
8. **Cross-platform mix**: ARM Pi devices + x86_64 Windows (Jade) + mixed architectures

## Goals (From Conversation)

### Primary Vision
Transform home lab into an **"AI Lab Manager"** that:
- Accepts conversational prompts to create new services/games
- Orchestrates automated deployment via Traefik/Arcane
- Uses GitHub for commit-based versioning of all configurations
- Self-audits and improves services during idle time
- Creates personalized experiences (e.g., child's game from morning prompt to afternoon play)

### Specific Capabilities Desired
1. "One request to create" - AI generates new service/game
2. Queue up updates/remarks - Conversational improvements over time
3. Auto-publish - Services launch on Arcane/Traefik automatically
4. Idle-time auditing - System improves itself during low-usage hours
5. Idea board integration - Vague prompts become spec documents/emails
6. Git-based versioning - All infrastructure as code in GitHub

## Current Service Inventory (What's Already Running)

### **Core Infrastructure**
- getArcane (orchestration/management)
- Traefik (reverse proxy/load balancer)
- Filebrowser (web-based file editor)
- Jellyfin (media streaming server)
- dozzle (container monitoring on each device)

### **Static Sites**
- historylabs.dev (on Pi 4 4GB altair)
- atlasofpelvicsurgery.org sites (on Pi 4 2GB vega)

### **Network Services**
- Firewalla Purple (DHCP, firewall, VPN, ad-blocking)
- ASUS XD5 access points (WiFi 6)