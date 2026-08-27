---
type: runbook
applies_to: "[[Services/Monitoring|Monitoring]]"
---

# Monitoring response

## Summary

Use Grafana to correlate service-health probes, host/container metrics, and Loki logs. A local alert proves a component that is still reachable from Firebat is unhealthy; it cannot prove the whole host or Internet is down.

## First response

1. Identify the alert’s service label and timestamp.
2. Open the Service Health dashboard to distinguish an endpoint failure from a container restart.
3. Check Server Overview for disk, memory, swap, CPU, and load pressure.
4. Query Loki by `container` for application logs or by `unit` for host systemd logs at the same time.
5. Follow the relevant service page and [[Runbooks/Compose stack operations|Compose stack operations]] if remediation requires a restart.

## Useful Loki labels

- `job="docker"` and `container="<container-name>"` for container logs.
- `job="journal"` and `unit="<systemd-unit>"` for persistent system journal logs.
- `job="unifi"` for UniFi log files.

## Escalation cues

Disk growth, repeated container restarts, unavailable MQTT, unavailable Zigbee2MQTT, unavailable ingress health, and certificate expiry are configured as high-priority signals. Verify the underlying condition before suppressing or changing an alert.
