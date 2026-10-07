# ADR 0001: Git is the source of truth (GitOps)

- **Status**: Accepted
- **Date**: initial

## Context

Service configuration currently lives partly in container state, partly in files on individual
hosts, and partly in the Traefik hot-folder. There is no single place that says "this is what
should be running." Changes are hard to review, reproduce, or roll back.

## Decision

Every service definition lives in Git. The repo is the desired state:

- `infra/services/<name>-compose.yml` - the deployable unit per service
- `infra/desired-state/desired-state.yaml` - the index of what should exist and where
- `infra/traefik/` - proxy config and dynamic routes

Changes are made by committing to Git; automation then syncs the fleet toward the committed state.

## Consequences

- **Positive**: reviewable history, easy rollback, reproducible, enables future automation.
- **Positive**: `desired-state.yaml` gives automation a machine-readable target.
- **Negative**: requires discipline (no hand-editing production without back-porting to Git).
- **Negative**: needs a sync mechanism (Arcane) and, eventually, secrets handling outside Git.

## Alternatives considered

- **Hand-managed hosts**: rejected - unreproducible, drift-prone.
- **Full IaC tooling (Terraform/Ansible)**: deferred - more power than needed now; may revisit if
  the fleet grows or drift becomes painful.
