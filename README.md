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

3. **Configure ArgoCD Root Path (for subpath ingress routing):**

   To expose ArgoCD under a subpath (e.g., `/argocd`), configure the root path:

   ```bash
   kubectl patch configmap argocd-cmd-params-cm -n argocd --type merge -p '{"data":{"server.rootpath":"/argocd"}}'
   ```

4. **Restart ArgoCD Server to apply the configuration:**

   ```bash
   kubectl rollout restart deployment argocd-server -n argocd
   ```

   Wait for the deployment to complete:

   ```bash
   kubectl rollout status deployment argocd-server -n argocd
   ```

### Accessing ArgoCD UI

To access the ArgoCD UI, you can port-forward the ArgoCD server:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then access the UI at: https://localhost:8080

### Getting Initial Admin Password

Retrieve the initial admin password:

**PowerShell:**
```powershell
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String((kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}")))
```

**Linux/macOS:**
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Default username is `admin`.

## Bootstrapping the Platform

After installing ArgoCD, you need to bootstrap the platform by applying the root application:

```bash
kubectl apply -f bootstrap/root-app.yaml
```

This root application will manage all other applications in the repository using the app of apps pattern.

## App of Apps Pattern

This repository uses the ArgoCD "app of apps" pattern, where a parent Application manages all child applications.
