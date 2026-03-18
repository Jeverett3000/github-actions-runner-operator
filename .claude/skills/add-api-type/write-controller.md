# Write the Controller

Controllers live in `controllers/`. Follow `controllers/githubactionrunner_controller.go` as the reference implementation.

## Controller File Structure

Create `controllers/<typename>_controller.go`:

```go
package controllers

import (
    "context"

    "k8s.io/apimachinery/pkg/runtime"
    ctrl "sigs.k8s.io/controller-runtime"
    "sigs.k8s.io/controller-runtime/pkg/client"
    "sigs.k8s.io/controller-runtime/pkg/log"

    garov1alpha1 "github.com/evryfs/github-actions-runner-operator/api/v1alpha1"
)

// MyTypeReconciler reconciles a MyType object
type MyTypeReconciler struct {
    client.Client
    Scheme *runtime.Scheme
}

//+kubebuilder:rbac:groups=garo.tietoevry.com,resources=mytypes,verbs=get;list;watch;create;update;patch;delete
//+kubebuilder:rbac:groups=garo.tietoevry.com,resources=mytypes/status,verbs=get;update;patch
//+kubebuilder:rbac:groups=garo.tietoevry.com,resources=mytypes/finalizers,verbs=update

func (r *MyTypeReconciler) Reconcile(ctx context.Context, req ctrl.Request) (ctrl.Result, error) {
    logger := log.FromContext(ctx)

    // Fetch the resource
    myType := &garov1alpha1.MyType{}
    if err := r.Get(ctx, req.NamespacedName, myType); err != nil {
        return ctrl.Result{}, client.IgnoreNotFound(err)
    }

    logger.Info("Reconciling", "name", req.Name)

    // TODO: implement reconciliation logic

    return ctrl.Result{}, nil
}

// SetupWithManager sets up the controller with the Manager
func (r *MyTypeReconciler) SetupWithManager(mgr ctrl.Manager) error {
    return ctrl.NewControllerManagedBy(mgr).
        For(&garov1alpha1.MyType{}).
        Complete(r)
}
```

## Register in main.go

Add to `main.go` after the existing controller setup:

```go
if err = (&controllers.MyTypeReconciler{
    Client: mgr.GetClient(),
    Scheme: mgr.GetScheme(),
}).SetupWithManager(mgr); err != nil {
    setupLog.Error(err, "unable to create controller", "controller", "MyType")
    os.Exit(1)
}
```

## RBAC Markers

The `//+kubebuilder:rbac:...` comments above the `Reconcile` function generate RBAC roles in `config/rbac/`. After adding/changing them, run:

```bash
make manifests
```

## Requeue Patterns

```go
// Requeue after a delay
return ctrl.Result{RequeueAfter: 30 * time.Second}, nil

// Requeue immediately
return ctrl.Result{Requeue: true}, nil

// Done, no requeue
return ctrl.Result{}, nil

// Error triggers requeue with backoff
return ctrl.Result{}, err
```

## Writing Tests

Add tests in `controllers/<typename>_controller_test.go` following `controllers/githubactionrunner_controller_test.go`. Use the envtest suite initialized in `controllers/suite_test.go`.
