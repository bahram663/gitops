# GitOps Repository

This repository contains Kubernetes manifests and configurations for deploying applications using GitOps principles with Kustomize.

## Repository Structure

```
your-gitops-repo/
├── production/           # Production environment
│   ├── values.yaml       # Production-specific values
│   └── kustomization.yaml # Production Kustomize config
├── test/                 # Test environment
│   ├── values.yaml       # Test-specific values
│   └── kustomization.yaml # Test Kustomize config
├── preview/              # Preview environments (dynamic)
│   ├── README.md         # Preview environment documentation
│   └── template/         # Template for new preview environments
│       ├── kustomization.yaml
│       └── values.yaml
└── base/                 # Base Kubernetes manifests
    ├── deployment.yaml   # Application deployment
    ├── service.yaml      # Kubernetes service
    ├── ingress.yaml      # Ingress configuration
    └── kustomization.yaml # Base Kustomize config
```

## Environments

### Production
- **Purpose**: Live production environment
- **Replicas**: 5
- **Resources**: High (256Mi memory, 1000m CPU)
- **Security**: Full security policies enabled
- **Monitoring**: Complete monitoring stack
- **SSL**: Enabled with Let's Encrypt

### Test
- **Purpose**: Testing and QA environment
- **Replicas**: 2
- **Resources**: Medium (128Mi memory, 500m CPU)
- **Security**: Relaxed for testing
- **Monitoring**: Basic monitoring
- **SSL**: Disabled

### Preview
- **Purpose**: Dynamic environments for feature branches and PRs
- **Replicas**: 1
- **Resources**: Low (64Mi memory, 250m CPU)
- **Security**: Minimal for development
- **Monitoring**: Disabled
- **SSL**: Disabled
- **TTL**: 24 hours (auto-cleanup)

## Usage

### Deploying to Production
```bash
kubectl apply -k production/
```

### Deploying to Test
```bash
kubectl apply -k test/
```

### Creating a Preview Environment
Preview environments are typically created automatically by CI/CD pipelines, but can be created manually:

```bash
# Create preview environment for a feature branch
mkdir preview/feature/my-feature
cp preview/template/* preview/feature/my-feature/
# Modify configurations as needed
kubectl apply -k preview/feature/my-feature/
```

## Configuration Management

### Base Configuration
The `base/` directory contains the core Kubernetes manifests that are shared across all environments. These include:
- Deployment specifications
- Service definitions
- Ingress configurations

### Environment-Specific Overrides
Each environment directory contains:
- `values.yaml`: Environment-specific configuration values
- `kustomization.yaml`: Kustomize configuration that references the base and applies environment-specific patches

### Kustomize Features Used
- **Resource Inheritance**: All environments inherit from `base/`
- **Strategic Merge Patches**: Environment-specific modifications
- **JSON Patches**: Fine-grained configuration changes
- **ConfigMap/Secret Generators**: Dynamic configuration generation
- **Image Tag Replacement**: Environment-specific image versions

## CI/CD Integration

This repository is designed to work with GitOps tools like:
- ArgoCD
- Flux
- Jenkins X
- Tekton

### Typical Workflow
1. Developer creates feature branch
2. CI/CD detects new branch
3. Preview environment is automatically created
4. Tests run against preview environment
5. Upon merge, deployment to test environment
6. After approval, deployment to production

## Security Considerations

- Production environments have full security policies enabled
- Test environments have relaxed policies for easier testing
- Preview environments have minimal security for development speed
- Secrets are managed through Kustomize secret generators
- TLS certificates are automatically managed in production

## Monitoring and Observability

- Production: Full monitoring stack (Prometheus, Grafana)
- Test: Basic monitoring (Prometheus only)
- Preview: Monitoring disabled for resource efficiency

## Contributing

1. Create a feature branch
2. Make changes to the appropriate environment configurations
3. Test changes in preview environment
4. Submit pull request
5. After review and approval, changes will be deployed to test then production

## Maintenance

- Preview environments are automatically cleaned up after 24 hours
- Regular updates to base images and dependencies
- Security patches applied across all environments
- Resource limits reviewed and adjusted as needed
