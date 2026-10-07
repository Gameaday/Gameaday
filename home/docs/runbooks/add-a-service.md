# Runbook: Add a Service

## Preconditions (do not skip)

Answer the Invisible Value Test in `service-catalog.md` for the service. If it does not clearly
save time/money, improve privacy, or improve reliability, **stop** - do not add it.

Confirm:
- [ ] Runs with <5 min/month maintenance
- [ ] Has an ARM64 image (if it runs on a Pi)
- [ ] Does not duplicate existing tooling (see ADR 0002)
- [ ] Fits the target device's headroom (`docs/resources.md`)
- [ ] Has a defined failure mode that does not disrupt family life

## Steps

1. **Create the compose file**
   - Copy `templates/service-compose-template.yml`
   - Save as `infra/services/<service-name>-compose.yml`
   - Fill in image (pin a version, not `latest`), ports, volumes, env, network
   - Add `restart: unless-stopped`
   - Add a comment noting the target device and ARM64 support

2. **Register it in desired state**
   - Add an entry to `infra/desired-state/desired-state.yaml`
   - Fields: `name`, `description`, `compose_file`, `enabled`, `namespace`, `target_device`,
     `update_strategy`

3. **Add a Traefik route (only if it needs to be reachable)**
   - Add `infra/traefik/dynamic/<service-name>.yml` using `example-route.yml` as a base
   - Prefer an internal-only host (`*.home.lab`); avoid public exposure

4. **Commit**
   - `git add` the compose file, desired-state entry, and route
   - Commit with a message stating the service and its family benefit

5. **Deploy via Arcane**
   - Let Arcane sync to desired state (or trigger the sync explicitly)
   - Confirm the container is running and healthy

6. **Verify**
   - Check logs via Dozzle
   - Confirm the route resolves (if applicable)
   - Confirm health check / alerting is wired

7. **Document**
   - Add a one-line entry to `service-catalog.md` if not already present
   - Note the success metric you will measure

## Rollback

- Set `enabled: false` in `desired-state.yaml` and commit, or
- `git revert` the commit that added the service.

## Definition of done

- Running, healthy, reachable if intended, monitored, and documented.
- A family member can be told the benefit in one sentence.
