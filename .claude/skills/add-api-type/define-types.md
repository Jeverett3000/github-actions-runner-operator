# Define API Types

Types live in `api/v1alpha1/`. Follow the existing `GithubActionRunner` pattern in `api/v1alpha1/githubactionrunner_types.go`.

## File Structure

Create `api/v1alpha1/<typename>_types.go`:

```go
package v1alpha1

import (
    metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
)

// MyTypeSpec defines the desired state
type MyTypeSpec struct {
    // +kubebuilder:validation:Required
    SomeField string `json:"someField"`

    // +kubebuilder:validation:Optional
    OptionalField *int `json:"optionalField,omitempty"`
}

// MyTypeStatus defines the observed state
type MyTypeStatus struct {
    Conditions []metav1.Condition `json:"conditions,omitempty"`
}

//+kubebuilder:object:root=true
//+kubebuilder:subresource:status

// MyType is the Schema for the mytypes API
type MyType struct {
    metav1.TypeMeta   `json:",inline"`
    metav1.ObjectMeta `json:"metadata,omitempty"`

    Spec   MyTypeSpec   `json:"spec,omitempty"`
    Status MyTypeStatus `json:"status,omitempty"`
}

//+kubebuilder:object:root=true

// MyTypeList contains a list of MyType
type MyTypeList struct {
    metav1.TypeMeta `json:",inline"`
    metav1.ListMeta `json:"metadata,omitempty"`
    Items           []MyType `json:"items"`
}

func init() {
    SchemeBuilder.Register(&MyType{}, &MyTypeList{})
}
```

## Register in groupversion_info.go

The `api/v1alpha1/groupversion_info.go` file handles scheme registration automatically via `SchemeBuilder`. Your `init()` call above is sufficient.

## Kubebuilder Markers

Common markers for the Spec fields:
- `// +kubebuilder:validation:Required` — field is required
- `// +kubebuilder:validation:Optional` — field is optional
- `// +kubebuilder:validation:Minimum=1` — numeric minimum
- `// +kubebuilder:validation:MaxLength=253` — string max length
- `// +kubebuilder:validation:Enum=foo;bar` — enum values

For the struct itself:
- `//+kubebuilder:object:root=true` — marks this as a root object
- `//+kubebuilder:subresource:status` — enables status subresource
- `//+kubebuilder:printcolumn:name="...",type="string",JSONPath=".spec.field"` — kubectl get columns

## After Defining Types

```bash
make generate   # generates zz_generated.deepcopy.go
make manifests  # generates config/crd/bases/<group>_<plural>.yaml
```
