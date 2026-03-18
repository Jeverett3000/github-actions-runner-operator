# Cluster Setup: Authentication and Secrets

Before deploying the operator, configure GitHub authentication in your cluster.

## Option 1: GitHub App (Recommended)

GitHub Apps provide better security and higher API rate limits.

### Create the GitHub App

1. Go to GitHub Settings → Developer Settings → GitHub Apps → New GitHub App
2. Set permissions:
   - **Repository-scoped**: Actions (Read/Write), Administration (Read/Write)
   - **Organization-scoped**: Self-hosted runners (Read/Write)
3. No callback URL or webhook secret needed
4. Note the **App ID** and download the **private key** (.pem file)

### Create the Kubernetes Secret

```bash
kubectl create secret generic github-runner-app \
  --from-literal=GITHUB_APP_INTEGRATION_ID=<app_id> \
  --from-file=GITHUB_APP_PRIVATE_KEY=<path/to/private-key.pem> \
  -n <operator-namespace>
```

### Inject into Operator Deployment

In `config/manager/manager.yaml`, add under the container spec:

```yaml
envFrom:
- secretRef:
    name: github-runner-app
```

## Option 2: Personal Access Token (PAT)

### Create the PAT

1. Go to GitHub Settings → Developer Settings → Personal Access Tokens
2. Grant `repo` scope (repository-level) or `admin:org` scope (organization-level)

### Operator-level PAT (applies to all CRs)

Set the `GH_TOKEN` environment variable on the operator deployment:

```bash
kubectl create secret generic github-runner-token \
  --from-literal=GH_TOKEN=<your-token> \
  -n <operator-namespace>
```

Then reference in the deployment's `envFrom`.

### CR-level PAT (per-resource override)

Create a secret in the same namespace as the `GithubActionRunner` CR:

```bash
kubectl create secret generic actions-runner \
  --from-literal=GH_TOKEN=<your-token> \
  -n <runner-namespace>
```

Reference it in the CR spec:

```yaml
spec:
  tokenRef:
    name: actions-runner
    key: GH_TOKEN
```

## Verify Secrets

```bash
kubectl get secrets -n <namespace>
kubectl describe secret github-runner-app -n <namespace>
```
