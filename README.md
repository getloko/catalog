# LoKO Catalog

<div align="center">
  <img src="https://avatars.githubusercontent.com/u/257918568?s=200" alt="LoKO Logo" width="200" height="200">
</div>

Official workload catalog for [LoKO](https://getloko.github.io/) - Local Kubernetes Oasis.

🌐 __Browse Catalog__: https://getloko.github.io/catalog/

📦 __Raw YAML__: `https://raw.githubusercontent.com/getloko/catalog/main/catalog.yaml`

## Overview

The LoKO Catalog is a curated collection of pre-configured Helm charts for databases, message queues, caches, and other infrastructure services optimized for local Kubernetes development.

## Catalog Structure

```
catalog/
├── catalog.yaml           # Main catalog with includes
├── repositories.yaml      # Helm repository definitions
├── components.yaml        # Internal components (Traefik, Registry, metrics-server)
├── examples.yaml          # Example workloads
└── workloads/
    ├── databases.yaml     # PostgreSQL, MySQL, MongoDB, DynamoDB Local (+ Web UIs)
    ├── cache.yaml         # Valkey, Memcached (+ phpCacheAdmin UI)
    ├── messaging.yaml     # RabbitMQ, NATS, Redpanda, ElasticMQ (+ UIs)
    ├── storage.yaml       # Garage (S3-compatible)
    ├── devops.yaml        # Forgejo, Forgejo Runner
    ├── devtools.yaml      # Mock SMTP/SMS
    ├── gitops.yaml        # ArgoCD, Flux Operator
    └── collaboration.yaml # Excalidraw
```

## Using the Catalog

### In LoKO CLI

The catalog is automatically included with LoKO:

```bash
# List available workloads
loko workload info postgres

# Add workload from catalog
loko workload add postgres

# Deploy workload
loko workload enable postgres --now
```

### Sync Remote Catalog

Sync this catalog to your local LoKO installation:

```bash
# Sync from GitHub catalog (default)
loko catalog sync --from-remote

# Sync from custom URL
loko catalog sync --from-remote https://raw.githubusercontent.com/getloko/catalog/main/catalog.yaml

# Preview changes without applying
loko catalog sync --from-remote --dry-run

# Force fresh fetch (skip cache)
loko catalog sync --from-remote --no-cache
```

The catalog is cached locally at `~/.config/loko/catalog/` and used by all LoKO commands.

### Direct YAML Access

Fetch catalog files programmatically:

```bash
# Main catalog
curl -O https://raw.githubusercontent.com/getloko/catalog/main/catalog.yaml

# Specific category
curl -O https://raw.githubusercontent.com/getloko/catalog/main/workloads/databases.yaml

# All workloads
for file in databases cache messaging storage devops devtools gitops collaboration; do
  curl -O https://raw.githubusercontent.com/getloko/catalog/main/workloads/${file}.yaml
done
```

## Catalog Format

Each workload entry defines:

```yaml
workloads:
  postgres:
    type: system
    description: "PostgreSQL relational database"
    defaults:
      namespace: loko-system
      ports: [5432]
    chart:
      repo: groundhog2k
      name: groundhog2k/postgres
      version: "1.6.1"
    endpoints:
      - name: client
        protocol: tcp
        port: 5432
    connections:
      default: "postgresql://postgres:${PASS}@${HOST}:5432/${DB}"
    secrets:
      - name: password
        type: password
        length: 16
    health_checks:
      - type: tcp
        port: 5432
```

## Available Workloads

### Databases

- __PostgreSQL__ - Popular SQL database with pgAdmin web UI
- __MySQL__ - MySQL database server with phpMyAdmin web UI
- __MongoDB__ - NoSQL document database with mongo-express web UI
- __DynamoDB Local__ - Local DynamoDB for development with dynamodb-admin web UI

### Cache & Key-Value Stores

- __Valkey__ - Redis-compatible in-memory cache
- __Memcached__ - High-performance distributed memory caching
- __phpCacheAdmin__ - Unified web UI for Redis/Memcached management

### Message Queues

- __RabbitMQ__ - Message broker with management UI
- __NATS__ - Cloud-native messaging with web dashboard
- __Redpanda__ - Kafka-compatible streaming platform with console UI
- __ElasticMQ__ - SQS-compatible in-memory message queue with web UI

### Object Storage

- __Garage__ - Lightweight S3-compatible storage

### DevOps & GitOps

- __Forgejo__ - Self-hosted Git service (Gitea fork)
- __Forgejo Runner__ - CI/CD runner for Forgejo Actions
- __ArgoCD__ - GitOps continuous delivery
- __Flux Operator__ - Flux CD operator for GitOps

### Collaboration

- __Excalidraw__ - Virtual whiteboard for sketching diagrams

### Development Tools

- __Mock SMTP/SMS__ - Testing server for email and SMS

## Contributing

### Adding a New Workload

1. Choose the appropriate category file in `workloads/`
2. Add your workload definition following the schema
3. Test with LoKO:

   ```bash
   loko workload add <your-workload>
   loko workload deploy <your-workload>
   ```

4. Submit a pull request

### Workload Schema

See the [LoKO Catalog Browser](https://getloko.github.io/_catalog/) for the complete catalog schema and workload reference.

### Guidelines

- Use official Helm charts when available
- Prefer groundhog2k charts for databases (lightweight, consistent)
- Include health checks for all workloads
- Provide connection strings with variable templates
- Document all exposed ports
- Test deployments before submitting

## Versioning

The catalog uses semantic versioning:

- __Major (1.x.x)__: Breaking changes to catalog format
- __Minor (x.1.x)__: New workloads or features
- __Patch (x.x.1)__: Updates to existing workload versions

Current version: `1.0.0`

## Catalog Updates

### Automated Updates

The catalog uses Renovate to track Helm chart updates:

```yaml
# renovate: datasource=helm depName=postgres repositoryUrl=https://groundhog2k.github.io/helm-charts
version: "1.6.1"
```

### Manual Updates

To update a chart version:

1. Find the latest version:

   ```bash
   helm search repo groundhog2k/postgres --versions
   ```

2. Update the version in the YAML file

3. Update the renovate comment

4. Test the deployment

5. Submit PR

## Browse Online

Visit the __[LoKO Catalog Browser](https://getloko.github.io/_catalog/)__ to explore workloads with search, filtering, and detailed information.

## Validation

Validate your catalog modifications:

```bash
# Install validation tool
pip install yamllint yq

# Validate YAML syntax
yamllint catalog.yaml workloads/*.yaml

# Check schema (coming soon)
./scripts/validate-catalog.sh
```

## Links

- __LoKO__: https://getloko.github.io/
- __Documentation__: https://getloko.github.io/catalog/
- __Catalog Browser__: https://getloko.github.io/_catalog/
- __Raw YAML__: https://raw.githubusercontent.com/getloko/catalog/main/catalog.yaml
