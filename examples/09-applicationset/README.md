# Example 09 — ApplicationSet

An **ApplicationSet** generates many `Application` resources from a single template
plus a **generator**. This example demonstrates the five most common generators,
each in its own subfolder.

Related docs: [17 — ApplicationSet](../../docs/17-applicationset.md).

## The generators

| # | Generator        | Folder                                     | What it does                                            |
|---|------------------|--------------------------------------------|---------------------------------------------------------|
| 1 | List             | [`01-list/`](01-list/)                      | One Application per hard-coded list element             |
| 2 | Cluster          | [`02-cluster/`](02-cluster/)                | One Application per registered cluster (label-selected) |
| 3 | Git — directory  | [`03-git-directory/`](03-git-directory/)    | One Application per directory matched in Git            |
| 4 | Git — file       | [`04-git-file/`](04-git-file/)              | One Application per config file matched in Git          |
| 5 | Matrix           | [`05-matrix/`](05-matrix/)                  | The cross-product of two child generators               |

All five reuse the Kustomize overlays from
[Example 06](../06-kustomize-overlays/) as their deployable content, so they
produce real, consistent Applications (`myapp-qa`, `myapp-prod`, …).

## Prerequisites

- Argo CD installed with the **`argocd-applicationset-controller`** running — it
  ships with the standard install used in
  [docs/07 — Installing Argo CD](../../docs/07-installing-argocd.md).
- The `02-cluster` example additionally needs one or more **registered clusters**
  (`argocd cluster add ...`); see its README.

## How to read an ApplicationSet

Every `applicationset.yaml` has the same shape:

```yaml
spec:
  generators:
    - <generator>          # produces a set of parameter maps
  template:                # an Application, with {{ }} placeholders
    metadata: { name: 'myapp-{{env}}' }
    spec: { ... }
```

The controller renders `template` once **per parameter set** the generator emits,
then keeps the resulting Applications in sync with the generator's output — add a
list element / directory / cluster and a new Application appears automatically.

## Try it (any subfolder)

```bash
kubectl apply -f examples/09-applicationset/01-list/applicationset.yaml
kubectl get applicationsets,applications -n argocd
argocd appset get myapp-list        # name varies per example
```
