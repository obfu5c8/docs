---
type: reference
---

# Repository map

## Summary

`/home/alan/server` is the version-controlled configuration repository for Firebat. This vault explains the system and how to operate it; it does not replace configuration review or source control.

| Repository location | Owns |
| --- | --- |
| `compose/services/<service>` | Compose definitions and service-specific configuration. |
| `compose/services/ingress/config/nginx` | Nginx global configuration and virtual hosts. |
| `host-config/systemd` | Managed Compose and maintenance units/timers. |
| `host-config/journald.conf.d` | Persistent journal retention policy. |
| `host-config/logrotate.d` | UniFi log-rotation policy. |
| `scripts/update-systemd.sh` | Unit installation, enablement, and activation. |
| `scripts/update-host-logging.sh` | Logging policy deployment and validation. |
| `dns/` | Terraform CNAME management for ingress hostnames. |

## Deployment entry points

From the repository root:

```sh
make systemd       # install/update managed systemd units
make host-logging  # apply journald and UniFi log retention policy
make dns           # show the DNS Terraform plan
make dns-apply     # apply a reviewed DNS change
```

Generated state, persistent service data, certificates, and credentials intentionally live outside the repository. See [[Infrastructure/Hosts/Firebat|Firebat]] for their locations.
