# Skill: Add a New API Type

Use this skill to add a new CRD (Custom Resource Definition) type to the operator.

## Overview

Adding a new API type involves two main steps:
1. **Define the Go types** in `api/v1alpha1/` — see [define-types.md](define-types.md)
2. **Write the controller** in `controllers/` — see [write-controller.md](write-controller.md)

## Prerequisites

- Go 1.19+
- `controller-gen` (installed automatically by `make generate`)
- A running Kubernetes cluster (for testing)

## Quick Steps

```bash
# 1. Define your types in api/v1alpha1/<newtype>_types.go
# 2. Run code generation
make generate

# 3. Regenerate CRD and RBAC manifests
make manifests

# 4. Write your controller in controllers/<newtype>_controller.go

# 5. Register the controller in main.go

# 6. Build and test
make manager
make test
```

## Naming Conventions

- Type names: `PascalCase` (e.g., `RunnerPool`)
- File names: `snake_case` (e.g., `runnerpool_types.go`)
- Controller file: `<typename>_controller.go`
- Group/version: `garo.tietoevry.com/v1alpha1`

Refer to the existing `GithubActionRunner` type as a reference implementation.
