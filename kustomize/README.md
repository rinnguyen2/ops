# Kustomize Structure

This directory contains Kustomize configurations for managing the myapp application across different environments.

## Directory Structure

```
kustomize/
├── base/
│   ├── kustomization.yaml    # Base kustomization configuration
│   └── values.yaml            # Base Helm values
└── overlays/
    ├── staging/
    │   └── kustomization.yaml # Staging environment overrides
    └── production/
        └── kustomization.yaml # Production environment overrides
```

## Environments

### Staging
- **Namespace**: `myapp-staging`
- **Replicas**: 1
- **Resources**:
  - Requests: 100m CPU, 128Mi memory
  - Limits: 200m CPU, 256Mi memory
- **Environment**: `STAGE=staging`

### Production
- **Namespace**: `myapp`
- **Replicas**: 2
- **Resources**:
  - Requests: 250m CPU, 256Mi memory
  - Limits: 500m CPU, 512Mi memory
- **Environment**: `STAGE=prod`
- **Autoscaling**: Disabled (can be enabled by setting autoscaling.enabled: true)

## Usage

### Testing locally with Kustomize

```bash
# Build staging configuration
kustomize build kustomize/overlays/staging

# Build production configuration
kustomize build kustomize/overlays/production
```

### Testing with Helm locally

```bash
# Test staging
helm template myapp ./app -f kustomize/overlays/staging/values.yaml

# Test production
helm template myapp ./app -f kustomize/overlays/production/values.yaml
```

### Deploy with ArgoCD

```bash
# Deploy staging
kubectl apply -f argocd/application-staging.yaml

# Deploy production
kubectl apply -f argocd/application-production.yaml
```

## Customizing per Environment

Each overlay can customize:
- Replica counts
- Resource limits/requests
- Environment variables
- Image tags
- Service types
- Autoscaling settings
- Any other Helm values

Simply edit the `valuesInline` section in the respective overlay's `kustomization.yaml`.

## Notes

- ArgoCD applications have automated sync enabled with self-heal and prune
- Namespaces will be created automatically via the CreateNamespace sync option
- Common labels are applied per environment for easy filtering
