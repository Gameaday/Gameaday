# Home Lab Information & Setup Audit

**Date**: initial audit
**Scope**: information accuracy, plan coherence, repository, hierarchy, resource realism.
**Purpose**: ensure we are set up correctly for the phased plan ahead, and that nothing
misleading is carried forward.

## Method

Reviewed every tracked file in this repo against the ground truth the owner provided
(hardware, network, family context, and stated goals). Checked for: factual accuracy,
internal consistency, truncation, dead/duplicated content, and realistic device placement.

## Findings & Resolutions

### A. Information (accuracy & consistency)

| # | Finding | Severity | Resolution |
|---|---------|----------|------------|
| A1 | `as-is-analysis.md` phase plan (Phases 1-5) conflicted with `docs/implementation-plan.md` (Phases 0-3). Two "sources of truth" for sequencing. | High | Rewrote `as-is-analysis.md` to point at the single phase plan in `docs/implementation-plan.md`. |
| A2 | `as-is-analysis.md` framed "AI Lab Manager" as the **primary vision**, contradicting the later decision that AI is a **stretch goal** seeded over time. | High | Reframed AI as a stretch goal; primary goal is now practical time/privacy/reliability/cost value. |
| A3 | `as-is-analysis.md` was missing most concrete hardware detail (RAM, storage, GPU, PSU, device names, per-device services). | High | Rewrote with full inventory; moved capacity detail to `docs/resources.md`. |
| A4 | `to-be-architecture-headless.md` was **truncated mid-diagram** and missing the phased plan. | Medium | Repaired; replaced the broken ASCII diagram with a maintained text description + links. |
| A5 | `service-catalog.md` was **incomplete** (stopped at Tier 2 item 7; no Tier 3/4, no template, no next steps). | Medium | Completed the catalog. |
| A6 | Family context (son 15 mo; daughter due ~19 mo) and touch-point reality (old laptops + phones only) were not captured. | Medium | Added to `as-is-analysis.md`. |

### B. Plan coherence

| # | Finding | Severity | Resolution |
|---|---------|----------|------------|
| B1 | No definition of "Phase 0 done" / exit criteria. | High | Added explicit exit criteria per phase in `docs/implementation-plan.md`. |
| B2 | Plan did not sequence observability *before* adding services. | Medium | Ordering fixed: GitOps -> observability -> backup verification -> first service. |
| B3 | No runbooks for the recurring operations the plan implies. | Medium | Added `docs/runbooks/`. |
| B4 | No record of key decisions. | Medium | Added ADRs in `docs/adr/`. |

### C. Repository

| # | Finding | Severity | Resolution |
|---|---------|----------|------------|
| C1 | Conversational scratch files (`note-to-user.md`, `response-to-user.md`, `update-on-repo-situation.md`) were tracked as if documentation. | Low | Removed (content preserved in this audit's history). |
| C2 | No `README.md`, `.gitignore`, or services README inside `home/`. | Medium | Added. |
| C3 | Migration artifacts (`home-repo.bundle`, `patches/`) mixed with source. | Low | Kept for now under `home/`, documented as migration artifacts; to be dropped after migration. |
| C4 | Push to the dedicated `Gameaday/home` repo is blocked (bot lacks write). | High (blocker) | Documented in `docs/migration.md`; work continues here and migrates later on a local instance. |

### D. Hierarchy

| # | Finding | Severity | Resolution |
|---|---------|----------|------------|
| D1 | Service filenames were inconsistent (`X-compose.yml` vs mixed naming). | Low | Standardized to `<service-name>-compose.yml`; documented in `infra/services/README.md`. |
| D2 | `infra/traefik/dynamic/` (the hot-reload dir) did not exist in the tree. | Medium | Added with an example route + `.gitkeep`. |

### E. Resources (realism) — the most important section

| # | Finding | Severity | Resolution |
|---|---------|----------|------------|
| E1 | "Local LLM endpoint on Pi 5 (sirius)" is **not realistic** (8 GB ARM, already busy). | High | Removed from sirius. LLM inference belongs on Jade (x86 + GPU) or a future dedicated node. |
| E2 | Jade GPU is an **AMD 9070 XT**. ROCm on Windows/WSL2 is immature; GPU-accelerated inference is not a given. | High | Flagged as a real constraint; AI stretch goal gated on proving GPU acceleration actually works on this host. |
| E3 | Pi 4 **2 GB** (vega) was assigned TURN/STUN + health checker + DNS/cache while also serving medical sites. | Medium | Reduced scope; moved DNS/cache off vega (Firewalla already does DNS/ad-block). |
| E4 | Pi 4 **4 GB** (altair) assigned apt-cacher-ng + site deploy + photo processing. | Low | Kept, but photo processing is a queue (best-effort), not real-time. |
| E5 | Charon (laptop, Nvidia 960M + Intel iGPU) assumed 24/7. | Medium | Marked on-demand; Jellyfin HW accel should use Intel QuickSync, not the old 960M. |
| E6 | No storage budget or power/noise/heat view. | Medium | Added `docs/resources.md`. |
| E7 | Firewalla already provides DNS + ad-block, yet a Pi-hole-equivalent was planned. | Medium | De-duplicated: local caching proxy yes; Pi-hole only if Firewalla proves insufficient. |

## Post-Audit Readiness

- **Single phase plan**: `docs/implementation-plan.md` is the only sequencing source.
- **Single inventory**: `docs/resources.md` is the capacity/constraint source; `as-is-analysis.md` is the narrative.
- **Decisions recorded**: `docs/adr/`.
- **Operations covered**: `docs/runbooks/`.
- **Migration path clear**: `docs/migration.md`.

## Decisions (resolved by owner)

1. **Firewalla replacement**: ~2028/2029, a ~10 GB gateway for future-proofing or at Purple EOL. Until then Purple owns DNS/ad-block (ADR 0004).
2. **AI node**: jade is NOT used as a network AI node for now. Only very lightweight models on charon or a Pi; large models deferred (ADR 0003).
3. **charon**: 24/7 server node, memory-bound. On-demand services typically one at a time (webtop day / Jellyfin evening). No process-heavy/memory-hungry service left on 24/7 (ADR 0005).
4. **Dedicated home repo**: migrate later on a local instance; work continues here (migration.md).
5. **Photo pipeline**: Synology Photos now (lowest friction). Evaluate Immich ~2030 with the open-source NAS move (ADR 0006).

## Conclusion

The information is now internally consistent, the plan has one owner document, the repo and
hierarchy are clean and documented, and resource assignments are realistic. The setup is ready
for Phase 0 execution. No service should be built before Phase 0 exit criteria are met.

| D3 | No stated mapping between `desired-state.yaml` entries and compose files. | Medium | Documented; added `target_device` field. |