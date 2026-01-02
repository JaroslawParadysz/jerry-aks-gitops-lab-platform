# ArgoCD Installation Guide

## Manual Installation Steps

This guide describes the manual steps required to install ArgoCD in your Kubernetes cluster.

### Prerequisites

- `kubectl` CLI tool installed and configured
- Access to a Kubernetes cluster with appropriate permissions

### Installation Steps

1. **Create the ArgoCD namespace:**

   ```bash
   kubectl create namespace argocd
   ```

2. **Install ArgoCD components:**

   ```bash
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

### Accessing ArgoCD UI

To access the ArgoCD UI, you can port-forward the ArgoCD server:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then access the UI at: https://localhost:8080

### Getting Initial Admin Password

Retrieve the initial admin password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Default username is `admin`.
