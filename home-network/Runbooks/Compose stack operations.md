---
type: runbook
applies_to: "[[Infrastructure/Hosts/Firebat|Firebat]]"
---

# Compose stack operations

## Summary

Compose stacks are operated through their `compose-<name>.service` systemd units. Make changes in `/home/alan/server`, validate the rendered Compose configuration, then reload/restart the relevant unit. Ingress is deliberately ordered after its upstream stacks.

## Inspect

```sh
sudo systemctl status compose-<stack>
sudo journalctl -u compose-<stack> --since '1 hour ago'
cd /home/alan/server/compose/services/<stack>
sudo docker compose ps
```

For broader health, inspect Grafana at `https://grafana.home.obfu5c8.me` and review the related service page in [[Services/Services|Services]].

## Deploy a stack change

1. Review the repository diff and the target service’s persistent storage implications.
2. Render and validate: `cd /home/alan/server/compose/services/<stack> && sudo docker compose config --quiet`.
3. Apply with `sudo systemctl reload-or-restart compose-<stack>`.
4. Confirm `systemctl status`, `docker compose ps`, container health checks, and the relevant public route where applicable.
5. For changed unit files, run `cd /home/alan/server && make systemd`; this updates unit links and safely reloads active stacks.

## Restart order

For an isolated application, restart its own unit first. If its network alias or container is recreated, restart/reload `compose-ingress.service` afterwards so the proxy is verified against its upstream. Do not stop ingress before its upstreams unless performing deliberate maintenance.

## Roll back

Return the repository to the last known-good committed configuration using normal Git workflow, then repeat the validation and systemd apply steps. Do not delete `/srv/compose/<app>` as a first response: it contains persistent data and may be the only copy.

## Common checks

- A failed `ExecStartPre` usually means required Compose environment variables are missing or invalid.
- An ingress failure after an application restart commonly means an expected external Docker network is absent or an upstream alias changed.
- Use `sudo docker compose logs --tail 200` inside the affected stack directory for container output.
