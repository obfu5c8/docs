---
type: service-group
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
depends_on: ["[[Services/Ingress|Ingress]]"]
---

# Smart home

## Summary

The Smart home stack groups Home Assistant, Mosquitto, Zigbee2MQTT, InfluxDB, a legacy Grafana instance, and Matter. It is managed by `compose-smarthome.service` and is a dependency of Ingress and Monitoring.

## Components

| Component | Purpose | Persistent location / special access |
| --- | --- | --- |
| Home Assistant | Automation platform | Repository-managed `config/hass`; runs privileged. |
| Mosquitto | Authenticated MQTT broker | `/srv/compose/smarthome/mosquitto/data`; TCP 1883 is published. |
| Zigbee2MQTT | Zigbee gateway | Repository-managed configuration; device `/dev/ttyUSB-zbdongle`. |
| InfluxDB | Smart-home time-series storage | `/srv/compose/smarthome/influx`; TCP 8086 is published. |
| Grafana | Existing smart-home dashboard instance | `/srv/compose/smarthome/grafana`; TCP 3000 is published. |
| Matter | Matter server | `/srv/compose/smarthome/matter`; uses host networking. |

## Dependencies and access

Home Assistant starts after Mosquitto and Zigbee2MQTT; Zigbee2MQTT also starts after Mosquitto. Home Assistant, Zigbee2MQTT, InfluxDB, Matter, and the legacy Grafana have ingress routes described in [[Infrastructure/Network/Access and ingress|Access and ingress]].

The Zigbee dongle path and Home Assistant’s privileged mode are host-specific operational dependencies: verify both after hardware or host changes. MQTT forbids anonymous connections and uses a password file stored in the service configuration; do not document its contents here.

## Migration note

The newer [[Services/Monitoring|Monitoring]] Grafana is intended to become the main observability UI. The legacy smart-home Grafana remains in place until its dashboards have been deliberately migrated.
