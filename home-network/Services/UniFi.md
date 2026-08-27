---
type: service
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
---

# UniFi

## Summary

The UniFi stack hosts the UniFi Network Application and its MongoDB database. It manages network equipment and is exposed through the Nginx route `https://unifi.home.obfu5c8.me`.

## Implementation

The controller depends on MongoDB. Persistent database data is stored at `/srv/compose/unifi/mongo`; controller logs are stored at `/srv/compose/unifi/logs` and collected by [[Services/Monitoring|Monitoring]]. Controller configuration remains in the repository at `compose/services/unifi/config/unifi`.

The controller publishes the ports needed for its web interface, STUN, access-point discovery, device communication, optional guest portal, throughput tests, and remote syslog. These are intentional controller interfaces; network firewall policy and physical topology remain to be documented.

## Observability

The controller’s internal HTTPS endpoint is blackbox-probed with certificate verification disabled because it uses an internal/self-signed certificate. Optional UnPoller telemetry requires a dedicated read-only UniFi account and an ignored local credentials file.
