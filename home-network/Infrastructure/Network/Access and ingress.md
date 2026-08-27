---
type: network-service
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
depends_on: ["[[Services/Ingress|Ingress]]", "[[Services/DNS|DNS]]"]
---

# Access and ingress

## Summary

Public service hostnames resolve through the external `firebat.home.obfu5c8.me.` record. Firebat’s Nginx container terminates TLS and proxies each configured site across an isolated Docker ingress network. HTTP is redirected to HTTPS.

## Published HTTPS applications

| Hostname | Upstream | Notes |
| --- | --- | --- |
| `cook.home.obfu5c8.me` | Cook app | Recipe application. |
| `grafana.home.obfu5c8.me` | Monitoring Grafana | Current main monitoring UI. |
| `influx.home.obfu5c8.me` | Smart-home InfluxDB | Existing smart-home data store UI/API. |
| `matter.home.obfu5c8.me` | Host port 5580 | Matter server path. |
| `qnap.home.obfu5c8.me` | `10.66.1.30` | Reverse proxy to QNAP; configuration still needs documenting. |
| `smart.home.obfu5c8.me` | Home Assistant | Includes a WebSocket route. |
| `syncthing.home.obfu5c8.me` | Syncthing GUI | GUI only; peer protocol is separate. |
| `unifi.home.obfu5c8.me` | UniFi controller | Upstream HTTPS uses its internal certificate. |
| `z2m.home.obfu5c8.me` | Zigbee2MQTT | Includes WebSocket-capable API route. |

## Bound host ports

Nginx binds TCP 80 and 443, plus TCP 1400 for Sonos callbacks forwarded to Home Assistant. Other host-published ports are service-specific: the Syncthing peer protocol uses TCP/UDP 22000; Mosquitto uses TCP 1883; InfluxDB uses TCP 8086; Grafana uses TCP 3000; and the UniFi controller publishes its controller, discovery, guest-portal, test, and logging ports. Treat published ports as part of the security boundary.

## DNS and certificates

[[Services/DNS|DNS]] declaratively manages the ingress CNAME records only. The zone, the `firebat` record, unrelated Gandi records, and ACME challenge TXT records are outside its scope.

A Let’s Encrypt wildcard certificate for `*.home.obfu5c8.me` is obtained and renewed using Certbot’s Gandi DNS plugin. The Gandi token stays in `/etc/letsencrypt/gandi.ini` with mode `0600`. Nginx mounts `/etc/letsencrypt` read-only so its `live/` symlinks continue to work after renewal. See [[Runbooks/Certificate renewal|Certificate renewal]].
