# Example Kubernetes Manifests

This directory contains example Kubernetes manifests that demonstrate both secure and insecure configurations for testing with KubeSec.

## Files

- `secure-pod.yaml` - A pod configuration following security best practices
- `insecure-pod.yaml` - A pod configuration with security issues (for testing)
- `networkpolicy.yaml` - Network policy for restricting traffic
- `poddisruptionbudget.yaml` - PDB for high availability

These manifests are designed to test the KubeSec workflow and demonstrate security scanning capabilities.
