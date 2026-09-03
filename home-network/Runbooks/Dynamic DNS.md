---
type: runbook
applies_to: "[[Services/DNS|DNS]]"
---

# Dynamic DNS

## Summary

`ddns.service` keeps `home.obfu5c8.me`'s Gandi A RRset aligned with the public WAN IPv4 reported by the local UniFi Network Application. `ddns.timer` runs it every five minutes, with a maximum 30-second randomized delay. The updater does not write Gandi when the record is already current.

The service has two root-only inputs:

| File | Purpose |
| --- | --- |
| `/etc/compose/ddns/unifi.env` | Dedicated read-only UniFi username, password, controller URL, site, and self-signed-TLS setting. |
| `/etc/letsencrypt/gandi.ini` | Existing Gandi personal-access token, shared with Certbot. |

Both files must be mode `0600` and never enter Git, a ticket, or a note.

## Deploy or change the service

From `/home/alan/server`, deploy repository and monitoring configuration with:

```sh
make systemd
```

This installs/enables the timer and reloads the monitoring Compose stack so node_exporter can collect textfile metrics. Run the updater once immediately after deployment or after changing its credentials:

```sh
make ddns
sudo systemctl status ddns.timer
sudo cat /var/lib/node_exporter/textfile_collector/ddns.prom
```

The first command can change the live Gandi A RRset if it differs from the WAN address. Check the `Updated DDNS record` or `already current` result before continuing.

## Routine verification

```sh
sudo systemctl status ddns.timer
sudo journalctl -u ddns.service --since '24 hours ago'
sudo cat /var/lib/node_exporter/textfile_collector/ddns.prom
```

In Grafana, open the **Dynamic DNS** dashboard. A healthy state has `ddns_last_run_success 1` and a recent successful-run timestamp. Grafana sends a Telegram Sev1 alert when the last run has failed for five minutes or the DDNS metrics are missing.

## Diagnose a failed update

1. Read `ddns.service` journal entries first; they identify whether UniFi or Gandi failed. Do not paste cookies, passwords, or tokens into notes.
2. Confirm `/etc/compose/ddns/unifi.env` exists, is root-only, and contains the expected controller URL and `default` site. Verify the dedicated account still has read-only UniFi Network access.
3. Confirm the controller is healthy and returns an `ok` WAN subsystem. The updater intentionally rejects private, loopback, carrier-grade NAT, and otherwise invalid IPv4 addresses instead of publishing them.
4. Confirm `/etc/letsencrypt/gandi.ini` exists, is root-only, and contains a valid Gandi token with permission to read and update the `home` A RRset. Do not create a duplicate token unless certificate and DDNS access must be deliberately separated.
5. Inspect `ddns.prom`; `ddns_last_run_success 0` confirms an updater failure, while a missing file usually means the service has not run or node_exporter was not redeployed after the feature was added.
6. After correcting the cause, run `make ddns`, confirm a successful journal entry and metrics, then wait for the Grafana alert to resolve.

## Limits

This is a local updater. If Firebat, the local Internet connection, or UniFi is unreachable, it cannot discover or publish a new address and may be unable to deliver its own Telegram alert. An independent external watcher is required to detect that class of outage.
