# resource-refresher

`resource-refresher` makes it easy to handle resources with parent-child relationships.

## How to use

Let's consider the process of creating a child resource from a parent resource.

```go
    // some process to create child resource 
    
	if _, err := controllerutil.CreateOrUpdate(context.TODO(), r.Client, copiedDeploy, func() error {
		copiedDeploy.Labels = child.labels
		copiedDeploy.Annotations = child.annotations
		copiedDeploy.Spec = child.spec

		// In order to support Update, set controller reference here
		return controllerutil.SetControllerReference(instance, copiedDeploy, r.Scheme)
	}); err != nil {
		return ctrl.Result{}, errors.WithStack(err)
	}
```

In this case, it is tedious to calculate the difference between the existing resources in Reconciler.

The resource-refresher will fill in the cluster and differences if you pass it the ideal resource state.

```go
	ref := refresh.New(r.client, r.scheme)
    if err := ref.Refresh(ctx, &parent, &child); err != nil {
        return errors.WithStack(err)
    }
}
```

Read more about the architecture using resource-refresher: [Go design theory learned from Kubernetes](https://speakerdeck.com/onsd/go-design-theory-learned-from-kubernetes) (in Japanese)

## Installation

Run `go get github.com/wantedly.com/resource-refresher` and import like

```go
import (
    refresh "github.com/wantedly/resource-refresher"
)
```

