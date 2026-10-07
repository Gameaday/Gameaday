# ADR 0005: On-demand services share charon; one heavy service at a time

- **Status**: Accepted
- **Date**: initial

## Context

charon (Asus ZenBook Pro, 16 GB) runs 24/7 as a server node. It hosts Jellyfin and a webtop, both
of which want the Intel iGPU. In practice their usage does not overlap: the webtop is used during
the work day, Jellyfin mostly in the evening as a family. So the real constraint is **memory**
more than CPU or heat. charon does not run hot unless the Nvidia GPU is used.

The owner will not leave process-heavy or memory-hungry services running 24/7, because that is a
bad neighbor to the other services - the same reason the dedicated game servers are left off.

## Decision

- charon is a 24/7 server node, but treated as a **shared, constrained** resource.
- Heavy/on-demand services on charon are scheduled or arbitrated so that **typically only one is
  active at a time** (webtop during the day, Jellyfin in the evening).
- Do **not** run process-heavy or memory-hungry services 24/7 on charon.
- The Nvidia 960M is not the default accelerator; use the Intel iGPU (QuickSync) for Jellyfin.
- Heavy, bursty workloads (game servers, large models) stay **off** until we have more hardware or
  a clean way to start/stop them on demand.

## Consequences

- **Positive**: preserves headroom for the services people actually rely on.
- **Positive**: avoids the bad-neighbor problem that keeps game servers off today.
- **Negative**: some services cannot simply "always be on"; they need scheduling/arbitration.
- **Follow-up**: prefer an on-demand start/stop mechanism over leaving things resident.

## Notes

- Memory is the binding constraint, not CPU/heat.
- Heat becomes a factor only if the Nvidia GPU is used; prefer not to.
