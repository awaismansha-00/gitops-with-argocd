# Example 10 — Multi-Cluster Hub-Spoke Model

This example runs Argo CD in one **hub** cluster and deploys the same demo app to
registered **spoke** clusters. It uses the ApplicationSet cluster generator to
create one `Application` per matching remote cluster.

Related docs: [22 — Multi-Cluster Hub-Spoke Model](../../docs/22-multicluster-hub-spoke-model.md).

## Layout

```text
examples/10-multicluster-hub/
├── app-project.yaml      # AppProject boundary for the fleet demo
└── applicationset.yaml   # Generates one Application per spoke cluster
```

The generated Applications deploy the existing Kustomize overlays from
[Example 06](../06-kustomize-overlays/):

- `environment=qa` deploys `examples/06-kustomize-overlays/overlays/qa`.
- `environment=prod` deploys `examples/06-kustomize-overlays/overlays/prod`.

## Prerequisites

- Argo CD is installed in the hub cluster.
- Your kubeconfig has contexts for the hub and each spoke cluster.
- The `argocd` CLI is logged in to the hub Argo CD API server.

Register spokes from the hub Argo CD CLI session:

```bash
argocd cluster add <spoke-qa-context> \
  --name spoke-qa \
  --label fleet=spoke \
  --label environment=qa

argocd cluster add <spoke-prod-context> \
  --name spoke-prod \
  --label fleet=spoke \
  --label environment=prod
```

## Try it

Apply these manifests to the hub cluster:

```bash
kubectl config use-context <hub-context>
kubectl apply -f examples/10-multicluster-hub/app-project.yaml
kubectl apply -f examples/10-multicluster-hub/applicationset.yaml
```

Check what ApplicationSet generated:

```bash
kubectl get applicationsets -n argocd
kubectl get applications -n argocd
argocd app list
```

Check the deployed workloads on the spoke clusters:

```bash
kubectl --context <spoke-qa-context> get deploy,svc -n qa
kubectl --context <spoke-prod-context> get deploy,svc -n prod
```

## Notes

The default local `in-cluster` target is not selected by this example. The
ApplicationSet selects cluster Secrets labelled `argocd.argoproj.io/secret-type=cluster`
and `fleet=spoke`, which normal remote cluster registrations have, while the
default local cluster does not.
