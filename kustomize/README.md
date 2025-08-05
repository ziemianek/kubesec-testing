# Kustomize Example

This directory contains a simple Kustomize configuration for deploying a web application across different environments.

## 📁 Structure

```
kustomize/
├── base/                           # Base configuration
│   ├── kustomization.yaml         # Base kustomization file
│   ├── namespace.yaml             # Namespace definition
│   ├── configmap.yaml             # Application configuration
│   ├── deployment.yaml            # Deployment with security settings
│   └── service.yaml               # Service definition
└── overlays/                      # Environment-specific overlays
    ├── development/               # Development environment
    │   ├── kustomization.yaml     # Dev kustomization
    │   ├── deployment-patch.yaml  # Dev-specific deployment changes
    │   └── configmap-patch.yaml   # Dev-specific config changes
    └── production/                # Production environment
        ├── kustomization.yaml     # Prod kustomization
        ├── deployment-patch.yaml  # Prod-specific deployment changes
        ├── configmap-patch.yaml   # Prod-specific config changes
        ├── hpa.yaml              # Horizontal Pod Autoscaler
        └── networkpolicy.yaml    # Network security policy
```

## 🚀 Usage

### Prerequisites

- kubectl installed and configured
- kustomize CLI (or use `kubectl -k`)

### Build and Preview

```bash
# Preview base configuration
kustomize build kustomize/base/

# Preview development overlay
kustomize build kustomize/overlays/development/

# Preview production overlay
kustomize build kustomize/overlays/production/
```

### Deploy to Kubernetes

```bash
# Deploy to development
kubectl apply -k kustomize/overlays/development/

# Deploy to production
kubectl apply -k kustomize/overlays/production/

# Or using kustomize directly
kustomize build kustomize/overlays/production/ | kubectl apply -f -
```

### Clean up

```bash
# Delete development deployment
kubectl delete -k kustomize/overlays/development/

# Delete production deployment
kubectl delete -k kustomize/overlays/production/
```

## 🔧 Configuration Differences

### Base Configuration
- **Namespace**: `webapp`
- **Replicas**: 2
- **Resources**: 500m CPU / 128Mi Memory (limits)
- **Environment**: base

### Development Overlay
- **Namespace**: `webapp-dev`
- **Name Prefix**: `dev-`
- **Replicas**: 1 (lower resource usage)
- **Resources**: 200m CPU / 64Mi Memory (limits)
- **Environment Variables**: 
  - `ENV=development`
  - `DEBUG=true`
- **Database**: `dev-db.local`
- **Cache**: Disabled for easier debugging

### Production Overlay
- **Namespace**: `webapp-prod`
- **Name Prefix**: `prod-`
- **Replicas**: 3 (high availability)
- **Resources**: 1000m CPU / 256Mi Memory (limits)
- **Environment Variables**:
  - `ENV=production`
  - `DEBUG=false`
- **Database**: `prod-db.example.com`
- **Cache**: Enabled with longer TTL
- **Additional Resources**:
  - Horizontal Pod Autoscaler (3-10 replicas)
  - Network Policy (security restrictions)
- **Enhanced Health Checks**: Longer intervals for stability

## 🛡️ Security Features

All configurations include security best practices:

- ✅ **Non-root containers** (`runAsNonRoot: true`)
- ✅ **Read-only root filesystem** (`readOnlyRootFilesystem: true`)
- ✅ **Dropped capabilities** (`drop: [ALL]`)
- ✅ **No privilege escalation** (`allowPrivilegeEscalation: false`)
- ✅ **Resource limits** (CPU, memory)
- ✅ **Health checks** (liveness, readiness probes)
- ✅ **Security contexts** (user/group IDs)
- ✅ **Network policies** (production only)

## 🧪 Testing with KubeSec

The generated manifests can be tested with KubeSec:

```bash
# Test development configuration
kustomize build kustomize/overlays/development/ > /tmp/dev-manifests.yaml
kubesec scan /tmp/dev-manifests.yaml

# Test production configuration
kustomize build kustomize/overlays/production/ > /tmp/prod-manifests.yaml
kubesec scan /tmp/prod-manifests.yaml
```

## 📋 Common Kustomize Commands

```bash
# Validate kustomization files
kustomize cfg fmt kustomize/

# List all resources in an overlay
kustomize cfg tree kustomize/overlays/production/

# Generate a new kustomization file
kustomize create --autodetect

# Add resources to kustomization
kustomize edit add resource new-resource.yaml

# Add patches to kustomization
kustomize edit add patch --path patch.yaml

# Set image tags
kustomize edit set image nginx:1.22-alpine
```

## 🔄 Customization Examples

### Adding a New Environment

1. Create a new overlay directory:
   ```bash
   mkdir -p kustomize/overlays/staging
   ```

2. Create `kustomization.yaml`:
   ```yaml
   apiVersion: kustomize.config.k8s.io/v1beta1
   kind: Kustomization
   
   namespace: webapp-staging
   bases:
   - ../../base
   namePrefix: staging-
   commonLabels:
     environment: staging
   ```

3. Add environment-specific patches as needed.

### Updating Image Versions

```bash
cd kustomize/overlays/production
kustomize edit set image nginx:1.22-alpine
```

### Adding Secrets

1. Create a secret generator in `kustomization.yaml`:
   ```yaml
   secretGenerator:
   - name: webapp-secrets
     literals:
     - database-password=secretpassword
   ```

2. Reference in deployment patch:
   ```yaml
   spec:
     template:
       spec:
         containers:
         - name: webapp
           envFrom:
           - secretRef:
               name: webapp-secrets
   ```

## 📚 Resources

- [Kustomize Documentation](https://kustomize.io/)
- [Kubectl Kustomize](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)
- [Kustomize Examples](https://github.com/kubernetes-sigs/kustomize/tree/master/examples)
