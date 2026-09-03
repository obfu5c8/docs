---
type: service
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
---

# DNS

## Summary

The repository’s `dns/` directory declaratively manages CNAME record sets for hostnames that are served by [[Services/Ingress|Ingress]]. It does not manage the zone itself, unrelated Gandi records, or Certbot’s temporary ACME TXT records.

The public A RRset `home.obfu5c8.me` is managed separately by the DDNS updater. It reads the WAN IPv4 address from [[Services/UniFi|UniFi]] and changes the Gandi record only when the address differs. Its 300-second TTL is intentional.

## Operation

Run `make dns` to initialise Terraform if needed and show a non-mutating plan. Run `make dns-apply` only after reviewing that plan. Local Terraform state stays in `dns/terraform.tfstate` and is intentionally ignored by Git.

The wrapper reads the existing Certbot Gandi credential from `/etc/letsencrypt/gandi.ini`; it may prompt for sudo solely to read that mode-`0600` file. Terraform runs as the invoking user. Never place the token in Terraform files, tfvars, or state backend configuration.


## Dynamic DNS

`ddns.timer` runs `ddns.service` every five minutes, with up to 30 seconds of randomized delay. The service authenticates to the local UniFi Network Application at `https://127.0.0.1:8443`, requests `/api/s/default/stat/health`, and accepts only an `ok` WAN subsystem with a valid public IPv4 address. It then reads the `home` A RRset through Gandi LiveDNS and writes it only when required.

UniFi credentials are held outside Git in `/etc/compose/ddns/unifi.env`, owned by root with mode `0600`. The service reuses the Gandi personal-access token in `/etc/letsencrypt/gandi.ini`; do not duplicate either secret. The dedicated UniFi account needs read-only Network access. See [[Runbooks/Dynamic DNS|Dynamic DNS]] for deployment, verification, and failure response.

## Adoption note

The configuration contains auditable import blocks for the CNAME records that predated Terraform management. The provider manages whole record sets, so do not add a second CNAME value for a managed hostname outside Terraform.


## DNS Names managed by terraform

- `qnap.home` - [[QNAP]]
- `smart.home` - [[Home Assistant]] ([[Smart home]])
- `z2m.home` - [[Zigbee2Mqtt]] ([[Smart home]])
- `syncthing.home` - [[Syncthing]]
- `unifi.home` - [[UniFi]]
- `cook.home` - [[Cook]]
- `grafana.home` - [[Monitoring]]
- 