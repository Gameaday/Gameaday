# ADR 0006: Storage roadmap - Synology now, open-source NAS around 2030

- **Status**: Accepted
- **Date**: initial

## Context

We have a Synology 1621+ (32 GB RAM, 32 TB, 1 TB NVMe cache). It currently runs Synology services
only (its Docker is restricted). Two pressures are coming:

1. **Drive failure probability** rises as the existing drives age.
2. **Capacity**: we will likely run out of space anyway.

The owner plans to move to a new open-source NAS platform around **2030**, when storage hardware
prices should have stabilized, and downgrade the Synology to a pure storage node.

## Decision

- **Now**: use **Synology Photos** for the photo/video pipeline. It is the lowest-friction option
  and already present - do not build a custom pipeline or deploy Immich yet.
- **~2030**: move to an open-source NAS platform; at that point evaluate **Immich** for photos.
- **Now**: the Synology remains the storage spine (snapshots + offsite archive + media/photos).
- The "Immutable Memory Vault" service is therefore about **verification and protection** of the
  Synology-hosted data, not about replacing Synology Photos.

## Consequences

- **Positive**: no new photo system to learn or maintain now; lowest family friction.
- **Positive**: avoids building on a platform we plan to replace.
- **Negative**: the Synology remains a dependency until the migration.
- **Risk**: drive failure - mitigated by the 3-2-1 rule and the backup verification runbook.

## Revisit trigger

When the 2030 NAS migration is planned, or earlier if a drive fails or capacity runs out.
