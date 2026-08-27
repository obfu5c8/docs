---
type: service
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
depends_on: ["[[Services/Smart home|Smart home]]", "[[Services/UniFi|UniFi]]", "[[Services/Cook|Cook]]", "[[Services/Monitoring|Monitoring]]", "[[Services/Syncthing|Syncthing]]"]
runbooks: ["[[Runbooks/Compose stack operations|Compose stack operations]]", "[[Runbooks/Certificate renewal|Certificate renewal]]"]
---

# Ingress

## Summary

Nginx is the sole HTTPS entry point for the self-hosted web applications. It joins each service’s dedicated ingress network, listens on 80/443/1400 on Firebat, redirects HTTP to HTTPS, and uses the shared wildcard certificate.

## Implementation

The Compose stack is at `/home/alan/server/compose/services/ingress`. Its Nginx configuration is version-controlled under `config/nginx`; site files are mounted as `/etc/nginx/sites-enabled`. Docker DNS (`127.0.0.11`) is used to resolve upstream aliases dynamically.

Ingress declares its service networks as external, so those networks must exist before Nginx starts. Systemd expresses this ordering by starting the ingress stack after Smart home, UniFi, Cook, Monitoring, and Syncthing.

See [[Infrastructure/Network/Access and ingress|Access and ingress]] for the complete currently configured route catalogue.

## Safety notes

- Do not expose an application’s management UI directly merely because it is reachable through an ingress network.
- Route changes require a Compose/Nginx configuration review and an ingress reload or restart.
- Certificate material is host-managed, not stored in this repository.
