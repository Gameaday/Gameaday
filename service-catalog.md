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
**Requirements**: Synology Photos/Immich, Hyper Backup to Glacier
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
**Requirements**: Fractal Terra/Jade or Asus ZenBook for HW transcode, Synology, Filebot/Sonarr
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
- Success: Time spent managing media library