# ADR 0002: One management plane (Arcane), one proxy (Traefik)

- **Status**: Accepted
- **Date**: initial

## Context

It is tempting to add overlapping tooling: Watchtower for updates, Portainer for management,
Pi-hole for DNS, a second reverse proxy, etc. Each addition is another thing to maintain and
another failure mode. The owner has explicitly said: do not add things just to add them.

## Decision

- **Arcane** is the single management/orchestration plane. It already handles container updates,
  so **no Watchtower**.
- **Traefik** is the single reverse proxy, using its hot-reload file provider.
- **Firewalla** owns DNS + ad-blocking, so **no Pi-hole** unless Firewalla proves insufficient.
- New tooling is only added if it *replaces* something or delivers value nothing else can.

## Consequences

- **Positive**: fewer moving parts, one place to look, less maintenance.
- **Positive**: no duplicate DNS/update/proxy systems fighting each other.
- **Negative**: we depend on Arcane's capabilities; gaps must be solved *within* it or explicitly.

## Review trigger

Revisit if Arcane cannot express a required capability, or if it becomes a bottleneck.
