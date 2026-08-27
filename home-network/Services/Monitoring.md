---
type: service
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
depends_on: ["[[Services/Ingress|Ingress]]"]
runbooks: ["[[Runbooks/Monitoring response|Monitoring response]]"]
---

# Monitoring

## Summary

Monitoring provides local metrics, logs, health probes, dashboards, and selected Telegram Sev1 alerts for Firebat’s services. Grafana is available only through [[Services/Ingress|Ingress]] at `https://grafana.home.obfu5c8.me`.

## Components and retention

| Component | Role | Retention / exposure |
| --- | --- | --- |
| VictoriaMetrics | Prometheus-compatible scrape and metrics storage | 30 days; internal |
| Grafana | Dashboards, metrics, logs, and alert UI | HTTPS through ingress only |
| Loki | Docker, host-journal, and UniFi logs | 14 days; internal |
| Alloy | Docker, journal, and UniFi log collector | Internal; read-only Docker socket is sensitive |
| node_exporter / cAdvisor | Host and container metrics | Internal |
| blackbox_exporter | HTTP, TCP, and TLS checks | Internal |
| UnPoller | Optional UniFi telemetry | Profile-gated and credentialed |

## What is checked

Blackbox checks Grafana, Cook, Home Assistant, Zigbee2MQTT, UniFi’s controller HTTPS endpoint, and the Mosquitto TCP endpoint. A TLS probe checks the Grafana public endpoint and drives certificate-expiry alerting. Docker lifecycle metrics identify core-service crash loops.

Grafana provisions VictoriaMetrics and Loki data sources plus host, container, and service-health dashboards. Telegram credentials are injected from `/etc/compose/monitoring/telegram.env`, not from the repository. The documented alert routing attaches Telegram directly to rules marked `severity=sev1`.

## Limits

This is local-only observability: it cannot report that Firebat itself or its Internet connection is unavailable. That requires an independent external watcher.
