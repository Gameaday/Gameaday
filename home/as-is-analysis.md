# Home Lab As-Is Analysis & Direction

## Current State Summary

**Network**: Fios → Firewalla Purple (FW/DHCP/Ad-block) → 2.5GB wired + ASUS XD5 WiFi 6

**Devices**:
- Pi 5 (sirius): getArcane, Traefik, Filebrowser
- Pi 4 4GB (altair): historylabs.dev site
- Pi 4 2GB (vega): atlasofpelvicsurgery.org sites
- Pi 3B+: Offline
- Pi Zero 2W: Unused
- Asus ZenBook (Charon): Jellyfin + webtop
- Fractal Terra/Jade: AI testing/workstation (Windows)
- Synology 1621+: Central storage

**Software**: getArcane (orchestration), Traefik (proxy), Docker (Linux), Jellyfin (media), dozzle (monitoring), Git (profile site)

**Key Assets Working**: 
- GitHub for profile site
- Traefik hot-reloading
- Arcane API + update management
- Jellyfin media server
- Network ad-blocking (Firewalla)

**Gaps to Address**:
- No infrastructure-as-code in Git
- No local LLM/ML pipeline
- No service generation templates
- Need GitHub write permissions for automation
- Cross-architecture service compatibility (ARM vs x86)

## Goals (From Conversation)

**Primary Vision**: AI Lab Manager that turns conversational prompts into deployed services via GitOps, with self-auditing and personalized experiences.

**Capabilities**:
1. One-request service creation (AI → Git → Deploy)
2. Conversational updates over time
3. Auto-publish via Arcane/Traefik
4. Idle-time self-auditing/improvement
5. Idea board → specs/emails
6. Git-based infrastructure versioning

## Phased Implementation Plan

**Phase 1 (Weeks 1-2)**: GitOps Foundation
- Move infra configs to Git repo
- Set up webhook for auto-deploy
- Verify Arcane desired state sync

**Phase 2 (Weeks 3-4)**: AI Orchestrator
- Configure n8n for prompt→workflow
- Build Prompt → Generate → Commit → Deploy flow
- Add GitHub token auth

**Phase 3 (Weeks 5-6)**: Service Templates
- Mini-game template (Phaser.js)
- Simple web service template (React/Node)
- Test deployments

**Phase 4 (Weeks 7-8)**: Autonomous Auditing
- Off-peak n8n cron jobs
- Health/checks + improvement PRs
- User review workflow

**Phase 5 (Weeks 9+)**: AI Model Integration
- Local LLM on Fractal Terra/Jade
- Offline-first with cloud fallback
- Full prompt→game cycle

## Next Immediate Steps

1. Explore Arcane API endpoints
2. Create `infra/` directory structure
3. Test Traefik hot-reload mechanism
4. Configure GitHub token for automation
5. Build first n8n workflow (prompt→deploy)

## Resource Allocation

- **Orchestrator**: Pi 5 (sirius) - n8n
- **LLM Inference**: Fractal Terra/Jade (Windows GPU)
- **Traefik/Git**: Pi 5 (sirius) - central
- **Asset Gen**: Fractal Terra/Jade (GPU) or cloud
- **Deployment Targets**: All Pis + Asus zenbook

## Enablers Present
✅ Traefik hot-folder  ✅ Arcane API  ✅ Git repo  ✅ Cross-device mgmt  ✅ SSD storage  ✅ Jellyfin deployed  ✅ Network ad-block  ✅ Arcane update mgmt

## Roadblocks
❌ Local LLM/ML  ❌ Service templates  ❌ Git write perms  ❌ Traefik→Arcane sync  ❌ ARM/x86 compat  ❌ Firewalla replacement path