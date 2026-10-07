# Home Lab Service Catalog

## Service Evaluation Framework

**Invisible Value Test** (must answer for any service):
1. Time/money saved? (Be concrete)
2. Recurring cost eliminated? (Name subscription service)
3. Privacy risk reduced? (What data stays home that previously left?)
4. Reliability problem solved? (What current frustration does this fix?)
5. Monitoring overhead? (Admin time to keep working?)
6. Failure impact? (If breaks tomorrow, what family disruption?)
7. "So What?" Test: What would family notice as better?

**Headless Suitability** (must all be yes):
- Runs autonomously with <5min/month maintenance?
- Improves something family actually uses daily/weekly?
- Enhances privacy without reducing convenience?
- Cheaper than alternative over 2 years? (Factor in time, not just dollars)
- Verifiable without logging in? (Automated health checks with simple alerts)

## Tier 1: Essential Quality of Life (Start Here)

### 1. Immutable Memory Vault
**Purpose**: Auto-backup family photos/videos to immutable storage
**Description**: Phone → Synology → encrypted → Glacier Deep Archive
**Requirements**: Synology Photos (now; Immich ~2030 per ADR 0006), Hyper Backup to Glacier
**Benefits**: 
- Saves: $9.99-$19.99/month (iCloud/Google Photos)
- Privacy: Media stays home unless explicitly shared
- Reliability: Protection against device loss/deletion
- Time: Eliminates manual photo organizing/backup
**Implementation**: Phase 1
- Headless: Yes (<2min/month verification)
- Success: % of family photos in immutable storage

### 2. Media Library Autonomy
**Purpose**: Self-hosted media library to replace streaming subs
**Description**: Jellyfin on Charon + auto-organization (HW accel)
**Requirements**: charon (QuickSync) for HW transcode, Synology storage, Filebot/Sonarr
**Benefits**:
- Saves: $50-100+/month (multiple streaming subs)
- Privacy: Viewing habits stay home
- Reliability: No surprise content removal
- Time: No browsing multiple services for content
**Implementation**: Phase 1
- Headless: Yes (<5min/month maintenance)
- Success: Media access time (seconds to find show)

### 3. Communication Reliability Booster
**Purpose**: Improve video call quality with distant family
**Description**: Jitsi TURN/STUN server on Vega for grandparent calls
**Requirements**: Pi 4 2GB (vega), Jitsi Meet with TURN/STUN config
**Benefits**:
- Saves: Tech support time for family members
- Privacy: Call data stays off third-party servers
- Reliability: More consistent call quality (less freezing/dropping)
- Time: Less frustration with calls
**Implementation**: Phase 1
- Headless: Yes (<2min/month monitoring)
- Success: Grandparent contact frequency/quality

### 4. Environmental Early Warning System
**Purpose**: Prevent home damage via environmental monitoring
**Description**: Pi Zero W sensors for temp/humidity/water leaks in key areas
**Requirements**: Multiple Pi Zero W's (nursery, basement, attic, under sinks), sensors, alerting to family chat
**Benefits**:
- Saves: Costly repairs from frozen pipes, mold, leaks
- Privacy: Environmental data stays home
- Reliability: Early warning prevents emergencies
- Time: No manual checking, faster response to issues
**Implementation**: Phase 1
- Headless: Yes (<2min/month, alert-only)
- Success: Number of prevented incidents (frozen pipes, mold, etc.)

### 5. Network Privacy & Efficiency Booster
**Purpose**: Improve network perf/privacy for all devices
**Description**: Pi-hole equivalent + local caching proxy (apt-cacher-ng)
**Requirements**: Pi 5 (sirius) for Pi-hole equiv and caching proxy
**Benefits**:
- Saves: Troubleshooting time, bandwidth costs
- Privacy: Prevents DNS-level tracking for all devices
- Reliability: Faster OS/package updates, reduced bandwidth
- Time: Less ad-related troubleshooting
**Implementation**: Phase 0 (Foundation)
- Headless: Yes (<2min/month dashboard checks)
- Success: Ads blocked/day, bandwidth saved

## Tier 2: High Value/Low Maintenance

### 6. Automated Household Operations
**Purpose**: Reduce maintenance burden through automation
**Description**: Automatic security updates, log aggregation, anomaly detection
**Requirements**: Update manager with rollback, centralized logging, log analysis
**Benefits**:
- Saves: Weekly update chores, security vulnerability windows
- Reliability: Systems stay patched with minimal intervention
- Time: Eliminates manual update time
- Peace: Automated security maintenance
**Implementation**: Phase 2
- Headless: Yes (<5min/month review)
- Success: % systems auto-updated, time spent on updates

### 7. Intelligent Media Management
**Purpose**: Automate media library maintenance/enhancement
**Description**: Commercial skipping, format conversion, duplicate detection
**Requirements**: Filebot, Comskip, ffmpeg, mediainfo tools
**Benefits**:
- Saves: Manual media management time
- Enhances: Media library usability/accessibility
- Reliability: Consistent experience across devices
- Flexibility: Auto format conversion for different devices
**Implementation**: Phase 2
- Headless: Yes (<5min/month oversight)

## Tier 3: Experimental / High Effort (validate a hypothesis first)

### 8. Learning & Development Helper
**Purpose**: Age-appropriate, ad-free educational content for the kids
**Description**: Curated educational videos/activities on old tablets, rotated weekly
**Requirements**: Old tablets, content curation, automated rotation, network ad-block (Firewalla)
**Benefits**:
- Provides: Constructive screen time alternative
- Privacy: No tracking of children's viewing
- Reliability: Consistent, curated content
- Time: Parents get breaks while kids learn
**Implementation**: Phase 3
- Headless: Yes (<5 min/month content refresh)
- Success: Voluntary usage by children
- **Hypothesis to validate**: kids actually choose it over alternatives

### 9. Creative Family Time Enabler
**Purpose**: Family bonding via guided creative projects
**Description**: Scheduled weekend AI-assisted projects; voice-cloned storytime
**Requirements**: very lightweight models on charon/Pi (jade NOT used for now, ADR 0003), ethical voice consent
**Benefits**:
- Enhances: Bonding; preserves family voices
- Privacy: Creative/voice data stays home
- Reliability: A recurring family ritual
**Implementation**: Phase 3 (gated on GPU validation, ADR 0003)
- Headless: Yes (adult-initiated, runs autonomously)
- Success: Family participation
- **Hypothesis to validate**: it is genuinely fun, not a chore

### 10. Proactive Home Management
**Purpose**: Predict/prevent issues from sensor trends
**Description**: Predictive alerts; shift flexible loads off-peak
**Requirements**: Sensor history (from Tier 1), trend analysis, smart plugs
**Benefits**:
- Prevents: Unexpected breakdowns
- Saves: Energy, extends equipment life
- Time: Planned maintenance instead of emergencies
**Implementation**: Phase 3
- Headless: Yes (<5 min/month review)
- Success: Reduction in emergency incidents
- **Hypothesis to validate**: predictions are actionable, not noise

## Tier 4: Generally Avoid (unless a compelling family-specific reason appears)

| Service | Why avoid | Alternative |
|---------|-----------|-------------|
| General game servers | Kids use established platforms with friends | Use those platforms |
| Public-facing websites | Privacy risk > benefit | Private family journal |
| Complex home automation | High maintenance, low reliability | Simple, purpose-specific automations |
| Crypto/mining | Resource theft from family use | Invest directly if desired |
| Anything needing constant attention | Violates zero-touch rule | Only truly autonomous services |
| Kubernetes / heavy orchestration | Overhead >> benefit at this scale | Docker Compose + Arcane |
| Bleeding-edge tech | Stability > novelty here | Proven solutions |
| Manual-data-entry trackers (e.g. grocery inventory) | Glues everyone to an app; must save more time than it costs | Only if frictionless and provably time-positive |

## Service Implementation Template

### Service name
- **Purpose (one sentence)**:
- **Invisible Value Test**: time/money saved? cost removed? privacy? reliability? maintenance? failure impact? "so what?":
- **Requirements**: hardware / software / config / dependencies:
- **Target device** (from `docs/resources.md`):
- **ARM64 image available?** (if Pi-bound):
- **Headless operation** (yes/no + est. min/month):
- **Success metric**:
- **Failure mode / graceful degradation**:
- **Phase**:
- **Open questions**:

## Next steps

1. Do not build services before Phase 0 exit criteria are met (`docs/implementation-plan.md`).
2. Start with the Tier 1 services, in the order the plan sets out.
3. For each: fill the template, add compose + desired-state entry, follow
   `docs/runbooks/add-a-service.md`.
4. After ~2 weeks of real use, run the family check-in and keep/fix/kill.

- Success: Time spent managing media library