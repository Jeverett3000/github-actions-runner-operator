# github-actions-runner-operator

A Kubernetes operator for scheduling GitHub Actions self-hosted runners as on-demand pods. Built with [Kubebuilder](https://kubebuilder.io/) and [controller-runtime](https://github.com/kubernetes-sigs/controller-runtime).

## Project Structure

```
api/v1alpha1/           # CRD type definitions (GithubActionRunner)
controllers/            # Reconciler logic
  githubapi/            # GitHub API client wrapper
config/
  crd/                  # Generated CRD manifests
  rbac/                 # RBAC role definitions
  samples/              # Example CR manifests
main.go                 # Operator entry point
Makefile                # Build and deployment targets
```

## Build Commands

```bash
make manager            # Build the operator binary to bin/manager
go build -o bin/manager main.go  # Equivalent direct build
make docker-build       # Build Docker image (tagged from git describe)
```

## Test Commands

```bash
make test               # Run all tests with coverage (downloads kubebuilder test assets)
go test ./...           # Run tests without coverage report
```

## Code Generation

Run these after modifying types in `api/v1alpha1/`:

```bash
make generate           # Regenerate DeepCopy methods and other generated code
make manifests          # Regenerate CRD, RBAC, and webhook manifests
```

## Format and Lint

```bash
make fmt                # Run go fmt ./...
make vet                # Run go vet ./...
```

## Deploying to a Cluster

```bash
make install            # Install CRDs into the configured cluster
make deploy             # Deploy the operator to the configured cluster
make uninstall          # Remove CRDs from the cluster
```

## Key Architecture

- **CRD**: `GithubActionRunner` (garo.tietoevry.com/v1alpha1) — defines desired runner pool configuration
- **Reconciler**: `controllers/githubactionrunner_controller.go` — scales runner pods based on GitHub job queue
- **GitHub API**: `controllers/githubapi/runnerapi.go` — polls job queue and manages runner registration
- **Auth modes**: GitHub App (preferred) or Personal Access Token (PAT)

## Development Notes

- Go 1.19, controller-runtime v0.13.0
- After changing API types, always run `make generate && make manifests`
- Tests use envtest (kubebuilder test framework) with K8s 1.22.0 assets
- Image registries: `quay.io/evryfs/` and `ghcr.io/evryfs/`
