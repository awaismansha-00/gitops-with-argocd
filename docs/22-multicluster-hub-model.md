# Multi-Cluster Hub Model

In a **hub-and-spoke** Argo CD setup, Argo CD runs in one management cluster
(`hub`) and deploys workloads into one or more registered workload clusters
(`spokes`). The hub holds the Git and cluster credentials; the spokes only receive
the Kubernetes resources Argo CD reconciles.

This is useful when you want one Argo CD control plane for a fleet of clusters.

## Model

```text
Git repo
   |
   v
Argo CD in hub cluster
   |
   +-- spoke-qa cluster
   |
   +-- spoke-prod cluster
```

Each spoke is registered with Argo CD. Argo CD stores registered clusters as
cluster Secrets in the `argocd` namespace. ApplicationSet can then discover those
Secrets and generate one `Application` per matching cluster.

## Register spoke clusters

Start with a kubeconfig that has contexts for the hub and spoke clusters:

```bash
kubectl config get-contexts
```

Install Argo CD only in the hub cluster:

```bash
kubectl config use-context <hub-context>
kubectl apply -f manifests/argocd-install/namespace.yaml
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Log in to Argo CD, then register each spoke context:

```bash
argocd cluster add <spoke-qa-context> \
  --name spoke-qa \
  --label fleet=spoke \
  --label environment=qa

argocd cluster add <spoke-prod-context> \
  --name spoke-prod \
  --label fleet=spoke \
  --label environment=prod

argocd cluster list
```

The `argocd cluster add` command creates the required service account/RBAC in the
target spoke cluster and stores the connection details in the hub Argo CD
namespace.

## Generate spoke Applications

[Example 10](../examples/10-multicluster-hub/) uses an ApplicationSet cluster
generator:

- `fleet=spoke` selects only workload clusters.
- `environment in (qa, prod)` chooses which overlay each cluster receives.
- `{{.server}}` sends the generated Application to the spoke cluster API server.
- `{{.nameNormalized}}` keeps generated Application names Kubernetes-safe.

Apply the project boundary first, then the ApplicationSet:

```bash
kubectl config use-context <hub-context>
kubectl apply -f examples/10-multicluster-hub/app-project.yaml
kubectl apply -f examples/10-multicluster-hub/applicationset.yaml
```

Verify from the hub:

```bash
kubectl get applicationsets -n argocd
kubectl get applications -n argocd
argocd app list
```

Then verify workloads from each spoke context:

```bash
kubectl --context <spoke-qa-context> get deploy,svc -n qa
kubectl --context <spoke-prod-context> get deploy,svc -n prod
```

## Boundaries

The AppProject in Example 10 allows this repo to deploy only into the `qa` and
`prod` namespaces. It allows any destination server because the spoke API server
URLs are registered dynamically, but the ApplicationSet selector is what keeps the
demo scoped to clusters labelled `fleet=spoke`.

For stricter production setups, use more specific AppProjects, separate projects
per fleet/team, and narrower resource whitelists.

## Related

- [21 — Troubleshooting](21-troubleshooting.md)

## References

- Argo CD cluster management — https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-management/
- Declarative cluster setup — https://argo-cd.readthedocs.io/en/latest/operator-manual/declarative-setup/
- ApplicationSet cluster generator — https://argo-cd.readthedocs.io/en/release-3.5/operator-manual/applicationset/Generators-Cluster/
