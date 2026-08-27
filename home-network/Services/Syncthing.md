---
type: service
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
storage: /srv/docs
runbooks: ["[[Runbooks/Compose stack operations|Compose stack operations]]"]
---

# Syncthing

## Summary

Syncthing is Firebat’s always-on synchronization peer for Obsidian vaults. Obsidian does not run on Firebat. The `home-network` vault is stored at `/srv/docs/home-network` and mounted in the container under `/docs/home-network`.

## Storage and configuration

- `/srv/compose/syncthing` holds generated configuration, device identity keys, index database, and runtime state. It is persistent but must not be committed or copied into this configuration repository.
- `/srv/docs` is mounted at `/docs`. Vault folders are added within the Syncthing GUI; adding a vault does not require a Compose edit.
- Syncthing runs as UID/GID 1000 with umask `007`. The host directories should be writable by that account.

## Connectivity

The management GUI is published only through [[Services/Ingress|Ingress]] at `https://syncthing.home.obfu5c8.me`; port 8384 is not published on the host. The peer protocol is published on TCP and UDP 22000.

Discovery and relaying are intentionally disabled. Edge devices should connect explicitly with `tcp://syncthing.home.obfu5c8.me:22000` and `quic://syncthing.home.obfu5c8.me:22000`.

## Vault policy

Configure server-side staggered file versioning for every vault, retaining versions on the same filesystem as the vault. Sync shared `.obsidian` settings and plugins, but ignore device-specific `workspace.json` and `workspace-mobile.json`.
