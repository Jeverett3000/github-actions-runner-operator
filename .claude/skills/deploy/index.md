# Skill: Deploy the Operator

Use this skill to deploy the github-actions-runner-operator to a Kubernetes cluster.

## Overview

Deployment involves two main steps:
1. **Set up authentication and secrets** — see [cluster-setup.md](cluster-setup.md)
2. **Install and apply the operator** — see [apply.md](apply.md)

## Prerequisites

- A running Kubernetes cluster with `kubectl` configured (`~/.kube/config`)
- Docker for building images (or a pre-built image)
- `kustomize` (installed automatically by `make`)
- GitHub App credentials **or** a Personal Access Token

## Quick Steps

```bash
# 1. Set up secrets (see cluster-setup.md)

# 2. Build and push image
make docker-build docker-push-ghcr

# 3. Install CRDs and deploy
make install deploy

# 4. Verify deployment
kubectl get pods -n <namespace>
kubectl get githubactionrunners -A
```

## Cleanup

```bash
make uninstall    # Remove CRDs and operator from the cluster
```
