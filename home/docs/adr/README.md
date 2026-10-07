# Architecture Decision Records (ADRs)

Short records of decisions that shape this home lab, so future-us knows *why*.

| ADR | Title | Status |
|-----|-------|--------|
| [0001](0001-git-as-source-of-truth.md) | Git is the source of truth (GitOps) | Accepted |
| [0002](0002-one-management-plane.md) | One management plane (Arcane), one proxy (Traefik) | Accepted |
| [0003](0003-ai-is-a-stretch-goal.md) | AI orchestration is a stretch goal, seeded over time | Accepted |
| [0004](0004-firewalla-owns-dns-adblock.md) | Firewalla owns DNS + ad-blocking (replace ~2028/2029, 10 GB) | Accepted |
| [0005](0005-on-demand-services-share-charon.md) | On-demand services share charon; one heavy service at a time | Accepted |
| [0006](0006-storage-roadmap.md) | Storage roadmap - Synology now, open-source NAS ~2030 | Accepted |

## Adding an ADR

Copy the format: title, status, date, context, decision, consequences, alternatives.
Keep it short. Supersede rather than delete when a decision changes.
