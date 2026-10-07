# ADR 0003: AI orchestration is a stretch goal, seeded over time

- **Status**: Accepted
- **Date**: initial

## Context

An early conversation drifted toward an ambitious "conversational AI lab manager" that generates
whole services/games from a prompt. That is exciting but: (a) hardware here cannot do full local
generation today, (b) it risks becoming a mega-project that consumes family time instead of
returning it, and (c) it can overshadow simpler services that would deliver value now.

The owner's core belief: spend time up front to get more time back later. Any effort that does
not pay off is a net negative.

## Decision

- AI orchestration is **not** a phase objective. It is a **stretch goal**.
- The architecture is designed so AI *can* be added later, but nothing depends on it.
- We seed the stretch goal with small, independently-useful changes now:
  - structured logging + metrics (Phase 0)
  - machine-readable service metadata (this repo)
  - templated, automated service deployment (n8n + templates)
- The stretch goal is **gated** on proving GPU-accelerated inference actually works on Jade
  (AMD 9070 XT on Windows/WSL2), or on a future dedicated node.

## Consequences

- **Positive**: near-term work stays practical and time-positive.
- **Positive**: when AI is viable, the groundwork (metadata, templates, automation) already exists.
- **Negative**: no "wow" AI demos in the near term. Accepted deliberately.

## Anti-patterns this prevents

- Building a mega-project before proving smaller value.
- Services that require meticulous manual upkeep (the "grocery inventory" trap).
- Chasing a shiny future feature at the expense of family time.
