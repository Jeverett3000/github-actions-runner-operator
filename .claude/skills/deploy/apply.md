# Apply: Install and Verify the Operator

After completing [cluster-setup.md](cluster-setup.md), install and verify the operator.

## Build the Image

```bash
# Set your image tag (defaults to git describe output)
export IMG=ghcr.io/evryfs/github-actions-runner-operator:my-tag

make docker-build          # Build locally
make docker-push-ghcr      # Push to GHCR
```

For local Kind cluster testing:
```bash
make load-kind-image       # Load image into Kind without pushing
```

## Install CRDs

```bash
make install
```

Verify CRDs were created:
```bash
kubectl get crds | grep tietoevry
# Expected: githubactionrunners.garo.tietoevry.com
```

## Deploy the Operator

```bash
make deploy
```

This uses kustomize to apply `config/default/` which includes the manager deployment, RBAC, and service.

Verify the operator pod is running:
```bash
kubectl get pods -n github-actions-runner-operator-system
kubectl logs -n github-actions-runner-operator-system -l control-plane=controller-manager
```

## Create a GithubActionRunner Resource

Apply a sample CR (edit namespace/org/repo as needed):

```bash
kubectl apply -f config/samples/garo_v1alpha1_githubactionrunner.yaml
```

Or create your own:

```yaml
apiVersion: garo.tietoevry.com/v1alpha1
kind: GithubActionRunner
metadata:
  name: runner-pool
  namespace: default
spec:
  organization: your-org
  repository: your-repo      # omit for org-level runners
  minRunners: 1
  maxRunners: 5
  podTemplateSpec:
    spec:
      containers:
      - name: runner
        image: ghcr.io/evryfs/github-actions-runner:latest
```

## Verify Runners

```bash
# Check CR status
kubectl get githubactionrunners -A
kubectl describe githubactionrunner runner-pool

# Check runner pods
kubectl get pods -l app=github-runner -A
```

## Teardown

```bash
make uninstall    # Removes CRDs and operator deployment
```
