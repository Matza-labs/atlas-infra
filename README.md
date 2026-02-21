# atlas-infra

Infrastructure as Code for **PipelineAtlas** — Docker, Kubernetes, Helm.

## Purpose

Contains all deployment configurations, docker-compose files for local development, Kubernetes manifests, and Helm charts for production.

## Quick Start (Local Development)

```bash
docker-compose up -d
```

This will start:
- All MVP services (`atlas-scanner`, `atlas-parser`, `atlas-graph`, `atlas-rule-engine`, `atlas-report`)
- Redis (for Redis Streams messaging)
- PostgreSQL (for graph/report persistence)

## Structure

```
atlas-infra/
├── docker-compose.yml      # Local dev: all services + Redis + PostgreSQL
├── docker/                 # Per-service Dockerfiles
│   ├── Dockerfile.scanner
│   ├── Dockerfile.parser
│   ├── Dockerfile.graph
│   ├── Dockerfile.rule-engine
│   └── Dockerfile.report
└── helm/                   # Kubernetes Helm charts (Phase 2)
```

## Related

See [docs/](../docs/) for full architecture and [docs/ROADMAP.md](../docs/ROADMAP.md) for phased rollout plan.
