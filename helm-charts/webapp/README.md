# WebApp Helm Chart

A simple Helm chart for deploying a secure web application on Kubernetes.

## Features

- **Security-focused**: Implements security best practices including:
  - Non-root user execution
  - Read-only root filesystem
  - Dropped capabilities
  - Security contexts
  - Resource limits
  - Health checks

- **Production-ready**: Includes:
  - Horizontal Pod Autoscaler (HPA)
  - ConfigMaps for configuration
  - Service Account with minimal permissions
  - Ingress support
  - Comprehensive health checks
  - Helm tests

## Installation

### Prerequisites

- Kubernetes 1.16+
- Helm 3.0+

### Install the Chart

```bash
# Add the repository (if published)
helm repo add webapp https://example.com/helm-charts

# Install the chart
helm install my-webapp ./helm-charts/webapp

# Or with custom values
helm install my-webapp ./helm-charts/webapp -f my-values.yaml
```

### Uninstall the Chart

```bash
helm uninstall my-webapp
```

## Configuration

The following table lists the configurable parameters and their default values:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `2` |
| `image.repository` | Image repository | `nginx` |
| `image.tag` | Image tag | `1.21-alpine` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `service.type` | Service type | `ClusterIP` |
| `service.port` | Service port | `80` |
| `service.targetPort` | Container port | `8080` |
| `ingress.enabled` | Enable ingress | `false` |
| `resources.limits.cpu` | CPU limit | `500m` |
| `resources.limits.memory` | Memory limit | `128Mi` |
| `resources.requests.cpu` | CPU request | `250m` |
| `resources.requests.memory` | Memory request | `64Mi` |
| `autoscaling.enabled` | Enable HPA | `false` |
| `securityContext.runAsNonRoot` | Run as non-root | `true` |
| `securityContext.runAsUser` | User ID | `1000` |

## Examples

### Basic Installation

```bash
helm install webapp ./helm-charts/webapp
```

### With Ingress Enabled

```yaml
# values-ingress.yaml
ingress:
  enabled: true
  className: "nginx"
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
  hosts:
    - host: webapp.example.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: webapp-tls
      hosts:
        - webapp.example.com
```

```bash
helm install webapp ./helm-charts/webapp -f values-ingress.yaml
```

### With Autoscaling

```yaml
# values-hpa.yaml
autoscaling:
  enabled: true
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 70
```

```bash
helm install webapp ./helm-charts/webapp -f values-hpa.yaml
```

## Security Features

This chart implements several security best practices:

1. **Non-privileged containers**: All containers run as non-root users
2. **Read-only filesystem**: Root filesystem is mounted read-only
3. **Dropped capabilities**: All Linux capabilities are dropped
4. **Resource limits**: CPU and memory limits are defined
5. **Security contexts**: Pod and container security contexts are configured
6. **Network policies**: Can be extended with network policies
7. **Service account**: Dedicated service account with minimal permissions

## Testing

The chart includes Helm tests to verify the deployment:

```bash
helm test my-webapp
```

## Monitoring

The application exposes a health endpoint at `/health` for:
- Kubernetes liveness probes
- Kubernetes readiness probes
- External monitoring systems

## Development

### Linting

```bash
helm lint ./helm-charts/webapp
```

### Template Validation

```bash
helm template webapp ./helm-charts/webapp --debug
```

### Dry Run

```bash
helm install webapp ./helm-charts/webapp --dry-run --debug
```

## Contributing

1. Make changes to the chart
2. Update the version in `Chart.yaml`
3. Test the changes with `helm lint` and `helm template`
4. Run KubeSec scan to ensure security compliance
5. Submit a pull request

## License

This Helm chart is licensed under the MIT License.
