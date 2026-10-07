# ADR 0004: Firewalla owns DNS + ad-blocking (defer replacement)

- **Status**: Accepted
- **Date**: initial

## Context

The Firewalla Purple provides firewall, DHCP, VPN, DNS, and network-wide ad-blocking. The owner
plans to eventually replace it with a higher-throughput option that does not rely on external
cloud management. A Pi-hole-equivalent was floated as a home-lab service.

## Decision

- Firewalla remains the owner of DNS + ad-blocking for now.
- We do **not** deploy a competing DNS/ad-block service (no Pi-hole) while Firewalla does the job.
- We *do* deploy a local **caching** proxy (e.g. apt-cacher-ng) because that is non-overlapping.
- The Firewalla replacement is a **deferred infrastructure project**, tracked as a decision item,
  not part of the service phases.

## Consequences

- **Positive**: avoids two systems fighting over DNS; less maintenance.
- **Positive**: frees Pi resources for services that actually add value.
- **Negative**: we remain dependent on a device the owner wants to replace. Acceptable short term.

## Decision update: replacement timeline (owner input)

- Target window: **2028/2029**.
- Replacement goal: a **10 GB gateway** for future-proofing, or replace when the Purple reaches EOL.
- Until then, the Purple remains the DNS/ad-block owner and we deploy no competing service.
- When it is replaced, decide then whether DNS/ad-block becomes a home-lab service.
