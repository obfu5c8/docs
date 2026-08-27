---
type: runbook
applies_to: "[[Services/Ingress|Ingress]]"
---

# Certificate renewal

## Summary

Let’s Encrypt renews the wildcard `*.home.obfu5c8.me` certificate through Certbot’s Gandi DNS plugin. `letsencrypt-renew.timer` checks daily. The Gandi credential remains in `/etc/letsencrypt/gandi.ini` with mode `0600`.

## Routine verification

```sh
sudo systemctl status letsencrypt-renew.timer
sudo journalctl -u letsencrypt-renew.service --since '7 days ago'
```

Grafana also probes the public TLS endpoint and alerts before expiration. Check its certificate-lifecycle alerts when investigating a warning.

## Diagnose a failed renewal

1. Read the `letsencrypt-renew.service` journal and Certbot logs; do not paste tokens into tickets or notes.
2. Verify the Gandi credential file exists, is readable only by root, and contains a still-valid least-privilege token.
3. Confirm DNS API access and that temporary `_acme-challenge` records can be created and removed.
4. Confirm Nginx mounts `/etc/letsencrypt` read-only and reload it after a successful renewal if the renewal workflow did not already do so.
5. Verify the served certificate expiry through Grafana’s TLS probe or an HTTPS client.

## Controlled first issue or migration

Use Let’s Encrypt staging first, then issue a single production replacement only after staging succeeds. The repository file `compose/services/ingress/CERTIFICATE.md` contains the exact approved commands. Keep that file concise and this runbook as the operational context.
