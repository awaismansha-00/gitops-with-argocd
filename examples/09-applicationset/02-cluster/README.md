# 09.02 — Cluster Generator

Creates one Application per **registered cluster** that matches a label selector.
This is how you roll the same workload across a fleet of clusters.

## Generates

One `myapp-<clusterName>` Application for each cluster labelled `environment=qa`,
deployed to that cluster's API server.

## Prerequisites

The cluster generator only sees clusters that Argo CD knows about:

```bash
# Register a cluster (adds a cluster Secret in the argocd namespace)
argocd cluster add <kube-context>
argocd cluster list
```

Argo CD auto-creates an `in-cluster` entry for its own cluster
(`https://kubernetes.default.svc`). To match this example on a single cluster, label
that cluster secret:

```bash
kubectl -n argocd label secret <cluster-secret> environment=qa
# (the in-cluster entry may be managed differently; add a real cluster to see it clearly)
```

## Try it

```bash
kubectl apply -f examples/09-applicationset/02-cluster/applicationset.yaml
kubectl get applicationset myapp-clusters -n argocd
kubectl get applications -n argocd
```

Remove the `selector` to target **all** registered clusters. Cluster labels are also
available as `{{metadata.labels.<key>}}` template parameters.
