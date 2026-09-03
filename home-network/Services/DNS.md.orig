---
type: service
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
---

# DNS

## Summary

The repository’s `dns/` directory declaratively manages CNAME record sets for hostnames that are served by [[Services/Ingress|Ingress]]. It does not manage the zone itself, the `firebat` target record, unrelated Gandi records, or Certbot’s temporary ACME TXT records.

## Operation

Run `make dns` to initialise Terraform if needed and show a non-mutating plan. Run `make dns-apply` only after reviewing that plan. Local Terraform state stays in `dns/terraform.tfstate` and is intentionally ignored by Git.

The wrapper reads the existing Certbot Gandi credential from `/etc/letsencrypt/gandi.ini`; it may prompt for sudo solely to read that mode-`0600` file. Terraform runs as the invoking user. Never place the token in Terraform files, tfvars, or state backend configuration.

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