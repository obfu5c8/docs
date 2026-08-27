---
type: service
host: "[[Infrastructure/Hosts/Firebat|Firebat]]"
depends_on: ["[[Services/Ingress|Ingress]]"]
---

# Cook

## Summary

Cook is the recipe application, exposed at `https://cook.home.obfu5c8.me`. Its Compose stack runs the application and a PostgreSQL database.

## Implementation

The application waits for PostgreSQL to become healthy, exposes its HTTP service only to Docker networks, and is health-checked at `/api/health`. Recipe photos and application data persist in `/srv/compose/cook/app_data`; PostgreSQL data persists in `/srv/compose/cook/postgres_data`.

The application receives its release tag and database credentials through environment variables; the stack will refuse to render if required values are absent. The image uses `pull_policy: never`, so deployment requires the desired image to already exist locally. Ollama, when used, is reached via Firebat’s host gateway at port 11434.

## Operational cautions

Treat both persistent directories as a single backup/recovery unit. Do not restart the database independently during normal application operation unless diagnosing an issue; begin with [[Runbooks/Compose stack operations|Compose stack operations]].
