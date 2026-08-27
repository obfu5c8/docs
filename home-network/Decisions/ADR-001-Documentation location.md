# ADR-001: Home-network documentation lives in the Obsidian vault

**Status:** Accepted — 2026-08-26

## Context

The configuration repository needs concise orientation and nearby implementation notes, but broad system explanation, operational context, cross-service relationships, and future network/QNAP documentation need room to grow.

## Decision

Use `/srv/docs/home-network` as the canonical Obsidian vault for home-network documentation. Organize it by infrastructure domain, service, runbook, reference, and decision—not by a deep single host hierarchy. Use links and frontmatter to connect a service to the host and components it depends on.

Keep repository READMEs concise and point them to the canonical vault pages. Keep version-controlled configuration in `/home/alan/server`.

## Consequences

- Documentation can be detailed without making configuration files or READMEs unwieldy.
- A service can be documented once even if it later moves to another host.
- The vault needs disciplined links, summaries, and regular updates to avoid becoming a disconnected note collection.
