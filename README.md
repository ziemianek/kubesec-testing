# KubeSec Testing Repository

This repository demonstrates Kubernetes security scanning using [KubeSec](https://kubesec.io/) with GitHub Actions. It includes example Helm charts and Kubernetes manifests to test security configurations.

## 🛡️ Features

- **Automated Security Scanning**: GitHub Actions workflow that scans Kubernetes manifests with KubeSec
- **Helm Chart Examples**: Production-ready Helm chart with security best practices
- **Security Test Cases**: Both secure and insecure manifest examples for testing
- **Comprehensive Coverage**: Scans deployments, pods, services, network policies, and more

## 📁 Repository Structure

```
kubesec-tessting/
├── .github/workflows/
│   └── kubesec.yaml           # GitHub Actions workflow for KubeSec scanning
├── helm-charts/
│   └── webapp/                # Example Helm chart with security best practices
│       ├── Chart.yaml         # Chart metadata
│       ├── values.yaml        # Default configuration values
│       ├── README.md          # Chart documentation
│       └── templates/         # Kubernetes manifest templates
│           ├── deployment.yaml
│           ├── service.yaml
│           ├── ingress.yaml
│           ├── configmap.yaml
│           ├── serviceaccount.yaml
│           ├── hpa.yaml
│           └── tests/
├── examples/                  # Example Kubernetes manifests
│   ├── secure-pod.yaml        # Pod with security best practices
│   ├── insecure-pod.yaml      # Pod with security issues (for testing)
│   ├── networkpolicy.yaml     # Network policy example
│   └── poddisruptionbudget.yaml
└── manifests/                 # Additional manifest examples
```

## 🚀 Getting Started

### Prerequisites

- Kubernetes cluster (local or cloud)
- Helm 3.x
- kubectl configured

### Deploy the Helm Chart

```bash
# Install the webapp chart
helm install my-webapp ./helm-charts/webapp

# Or with custom values
helm install my-webapp ./helm-charts/webapp -f custom-values.yaml

# Check the deployment
kubectl get all -l app.kubernetes.io/instance=my-webapp
```

### Run Security Tests Locally

```bash
# Install KubeSec locally
curl -sSX POST --data-binary @examples/secure-pod.yaml https://v2.kubesec.io/scan

# Or using Docker
docker run -it --rm -v $(pwd):/workspace kubesec/kubesec:latest scan /workspace/examples/secure-pod.yaml
```

## 🔍 Security Scanning Workflow

The GitHub Actions workflow (`.github/workflows/kubesec.yaml`) automatically:

1. **Detects Changes**: Scans only modified YAML files in pull requests
2. **Filters Manifests**: Identifies Kubernetes manifests by checking for `apiVersion` and `kind` fields
3. **Runs KubeSec**: Analyzes each manifest for security issues
4. **Reports Results**: Posts detailed security scan results as PR comments
5. **Enforces Standards**: Fails the check if security issues are found

### Workflow Features

- ✅ **Smart Filtering**: Only scans actual Kubernetes manifests
- 📊 **Detailed Reporting**: Shows security scores and recommendations
- 🔄 **Automated Comments**: Posts results directly to pull requests
- 🛑 **Quality Gates**: Blocks merging if security issues are detected
- 🧹 **Cleanup**: Automatically cleans up resources after scanning

## 📋 Security Best Practices Demonstrated

The example manifests and Helm chart demonstrate:

### Container Security
- ✅ Non-root user execution (`runAsNonRoot: true`)
- ✅ Read-only root filesystem (`readOnlyRootFilesystem: true`)
- ✅ Dropped capabilities (`drop: [ALL]`)
- ✅ No privilege escalation (`allowPrivilegeEscalation: false`)
- ✅ Specific user/group IDs (`runAsUser`, `runAsGroup`)

### Resource Management
- ✅ CPU and memory limits
- ✅ Ephemeral storage limits
- ✅ Resource requests for proper scheduling

### Pod Security
- ✅ Security contexts at pod and container level
- ✅ Seccomp profiles (`seccompProfile: RuntimeDefault`)
- ✅ No service account token mounting (`automountServiceAccountToken: false`)
- ✅ Dedicated service accounts

### Network Security
- ✅ Network policies for traffic restriction
- ✅ Proper port configurations
- ✅ No host network usage

### Configuration Security
- ✅ ConfigMaps for configuration (not hardcoded secrets)
- ✅ Proper volume mounts
- ✅ EmptyDir volumes with size limits

## 🧪 Testing Examples

### Secure Configuration (examples/secure-pod.yaml)
- Should receive a positive KubeSec score
- Implements all security best practices
- Safe for production deployment

### Insecure Configuration (examples/insecure-pod.yaml)
- Will receive a negative KubeSec score
- Contains multiple security issues:
  - Missing resource limits
  - Uses `latest` image tag
  - Mounts Docker socket and host filesystem
  - Hardcoded secrets in environment variables
  - Uses host networking, PID, and IPC namespaces

## 📈 Interpreting KubeSec Results

- **Positive Score (≥0)**: Configuration follows security best practices ✅
- **Negative Score (<0)**: Security issues detected that need attention ❌

Example secure result:
```json
{
  "score": 7,
  "message": "Passed with a score of 7 points",
  "advise": [
    {
      "selector": "containers[] .securityContext .runAsNonRoot == true",
      "reason": "Force the container to run as a non-root user"
    }
  ]
}
```

## 🛠️ Development

### Adding New Manifests

1. Create your Kubernetes manifest in the appropriate directory
2. Ensure it includes `apiVersion` and `kind` fields
3. Test locally with KubeSec
4. Create a pull request to trigger automated scanning

### Updating the Helm Chart

1. Modify templates in `helm-charts/webapp/templates/`
2. Update `values.yaml` if adding new configuration options
3. Increment version in `Chart.yaml`
4. Test with `helm lint` and `helm template`
5. Create PR for automated testing

### Local Development Commands

```bash
# Lint the Helm chart
helm lint helm-charts/webapp

# Generate templates for inspection
helm template test-release helm-charts/webapp --debug

# Validate Kubernetes manifests
kubectl apply --dry-run=client -f examples/

# Run KubeSec locally
kubesec scan examples/secure-pod.yaml
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Add or modify manifests/charts
4. Ensure KubeSec tests pass
5. Submit a pull request

## 📚 Resources

- [KubeSec Documentation](https://kubesec.io/)
- [Kubernetes Security Best Practices](https://kubernetes.io/docs/concepts/security/)
- [Helm Chart Best Practices](https://helm.sh/docs/chart_best_practices/)
- [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.