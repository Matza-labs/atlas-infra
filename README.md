# atlas-infra ✅ Complete

Infrastructure as Code for **PipelineAtlas** — Docker, Kubernetes, Helm.

## Purpose

Contains all deployment configurations: Docker Compose for local development and PoC deployments, and Kubernetes Helm charts for production.

## Quick Start (Docker Compose)

```bash
# From the atlas-infra directory
docker compose up

# Or from the repo root
docker compose -f atlas-infra/docker-compose.yml up
```

This starts all 10 services + Redis + PostgreSQL:
- Redis: `localhost:6379`
- PostgreSQL: `localhost:5432` (db: `pipelineatlas`, user/pass: `postgres`)
- API: `http://localhost:8000`
- UI: `http://localhost:3000`

## Structure

```
atlas-infra/
├── docker-compose.yml      # Local dev: all services + Redis + PostgreSQL
├── docker/                 # Per-service Dockerfiles (Phase 1 worker services)
│   ├── Dockerfile.scanner  # atlas-scanner stream consumer
│   ├── Dockerfile.parser   # atlas-parser stream consumer
│   ├── Dockerfile.graph    # atlas-graph stream consumer
│   ├── Dockerfile.rule-engine  # atlas-rule-engine stream consumer
│   └── Dockerfile.report   # atlas-report stream consumer
└── helm/                   # Kubernetes Helm charts (production)
    ├── Chart.yaml
    ├── values.yaml
    └── templates/
        ├── deployment-api.yaml      # atlas-api deployment
        ├── deployment-ui.yaml       # atlas-ui deployment
        ├── deployment-workers.yaml  # scanner, parser, graph, rules, report, log-analyzer
        ├── service-api.yaml         # API service (ClusterIP)
        ├── service-ui.yaml          # UI service (ClusterIP + Ingress)
        ├── redis.yaml               # Redis StatefulSet
        └── postgres.yaml            # PostgreSQL StatefulSet
```

## Helm Deployment (Kubernetes)

```bash
# Install
helm install pipelineatlas ./helm \
  --set api.env.ATLAS_JWT_SECRET="<your-secret>" \
  --set api.env.DB_URL="postgresql://..." \
  --set api.env.REDIS_URL="redis://..."

# Upgrade
helm upgrade pipelineatlas ./helm --reuse-values

# Uninstall
helm uninstall pipelineatlas
```

Key values in `values.yaml`:
- `replicaCount.*` — worker/API replica scaling
- `image.tag` — service image tag
- `api.env.*` — environment variable overrides

## Air-Gapped / Offline Deployment

```bash
# Build images
docker compose build

# Export images
docker save pipelineatlas/api:latest | gzip > pipelineatlas-api.tar.gz
# (repeat for each service)

# Load on target machine
docker load < pipelineatlas-api.tar.gz
```

## Related

See [docs/ARCHITECTURE.md](../docs/ARCHITECTURE.md) for the full stream pipeline diagram and [docs/SECURITY.md](../docs/SECURITY.md) for the security deployment checklist.
