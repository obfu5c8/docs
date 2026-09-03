---
type: host
role: always-on-home-server
services: [ingress, smarthome, unifi, cook, monitoring, syncthing]
---

# Firebat

## Summary

Firebat is the current always-on home-server host. It runs Docker Compose applications managed as systemd services, terminates HTTPS traffic through [[Infrastructure/Network/Access and ingress|Nginx ingress]], and acts as the permanent Syncthing peer for Obsidian vaults.

## Responsibilities

- Run the Compose stacks listed in [[Services/Services|Services]].
- Keep the `*.home.obfu5c8.me` HTTPS applications available.
- Persist application data in `/srv/compose/<application>`.
- Hold shared documentation vaults in `/srv/docs/<vault>`.
- Collect local metrics and logs through [[Services/Monitoring|Monitoring]].

## Layout

| Location | Purpose | Ownership / handling |
| --- | --- | --- |
| `/home/alan/server` | Version-controlled Compose, systemd, DNS, and host-policy configuration | Change through Git and deploy using the documented commands. |
| `/srv/compose` | Persistent, non-versioned application data | Back up separately; do not commit it. |
| `/srv/docs/home-network` | This Obsidian vault | Syncthing-managed Markdown and attachments. |
| `/etc/compose/monitoring` | Monitoring secrets such as Telegram credentials | Root-owned, mode `0600`; never commit. |
| `/etc/letsencrypt` | Certificate material and Gandi DNS credential file | Sensitive host state; Nginx mounts certificate files read-only. |

## Service management

The stack units are `compose-{cook,ingress,monitoring,smarthome,syncthing,unifi}.service`. The ingress unit starts after the other stacks, because it attaches to their externally named ingress networks.

Use `systemctl status compose-<stack>` to inspect one stack, and `sudo systemctl restart compose-<stack>` for a controlled restart. See [[Runbooks/Compose stack operations|Compose stack operations]] before changing a stack.

The repository’s `make systemd` runs the deployment script: it updates unit symlinks, reloads systemd, enables configured units, and reloads or starts the non-timer units. `make host-logging` applies journald and UniFi log-rotation policy.

## Scheduled maintenance

- `docker-prune.timer` runs weekly, with up to two hours of randomized delay, and removes unused Docker resources.
- `letsencrypt-renew.timer` runs daily, also randomized by up to two hours, to renew the wildcard certificate when Certbot considers it due.

## Observability

[[Services/Monitoring|Monitoring]] retains metrics for 30 days and logs for 14 days. Host journald retention is capped at 1 GiB, preserves 2 GiB free disk space, and limits log age to 14 days. UniFi logs rotate daily, at 25 MiB maximum per file, retaining 14 compressed rotations.
