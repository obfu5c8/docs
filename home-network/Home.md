---
type: index
tags: [home-network, documentation]
---

# Home network

This vault is the operational source of truth for the home network. It contains the system-level explanation and runbooks; the configuration repository at `/home/alan/server` remains the source of truth for version-controlled configuration.

## At a glance

The current always-on host is [[Infrastructure/Hosts/Firebat|Firebat]]. It runs Docker Compose stacks managed by systemd, provides HTTPS ingress for self-hosted applications, and is the always-on Syncthing peer for this vault. Persistent application data lives below `/srv/compose`; the vault lives at `/srv/docs/home-network`.

## Start here

- [[Infrastructure/Hosts/Firebat|Firebat]] — host responsibilities, storage layout, and service inventory.
- [[Infrastructure/Network/Access and ingress|Access and ingress]] — published names, TLS, and routing boundary.
- [[Services/Services|Services]] — application catalogue.
- [[Runbooks/Runbooks|Runbooks]] — routine operations and fault response.
- [[Reference/Repository map|Repository map]] — where configuration is managed.

## Documentation conventions

- Put a concise **Summary** first on every page; follow it with detail and links.
- Link to the thing being discussed (`[[Infrastructure/Hosts/Firebat|Firebat]]`, `[[Services/Syncthing|Syncthing]]`) rather than duplicating its description.
- Record passwords, tokens, recovery codes, and private keys in the password manager, never in this vault or repository.
- Use `[[Runbooks/Runbooks|runbooks]]` for repeatable procedures, and add a decision record when a choice needs its rationale preserved.

## Known gaps

Network hardware/topology, QNAP configuration, backup destinations, and recovery testing have not yet been documented. Placeholder pages exist only where current configuration refers to them; fill them from observed configuration rather than assumptions.
