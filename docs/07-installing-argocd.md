# Installing Argo CD

This guide installs Argo CD using the official upstream manifests. See
[`manifests/argocd-install/`](../manifests/argocd-install/) for the reusable YAML.

## Prerequisites

- A running Kubernetes cluster and `kubectl` configured to reach it
  (e.g. kind, minikube, EKS, GKE, AKS).
- The `argocd` CLI (optional but recommended) —
  https://argo-cd.readthedocs.io/en/stable/cli_installation/

## 1. Create the namespace

Argo CD is conventionally installed into the `argocd` namespace:

```bash
kubectl create namespace argocd
# or, declaratively:
kubectl apply -f manifests/argocd-install/namespace.yaml
```

## 2. Install Argo CD

Apply the official install manifest into the namespace:

```bash
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

> Pin to a specific release instead of `stable` for reproducibility, e.g.
> `.../argo-cd/v2.13.0/manifests/install.yaml`. Record the version you use.

There is also a `namespace-install.yaml` variant for installing Argo CD scoped to a
single namespace (no cluster-wide RBAC).

## 3. Verify the installation

Wait for all pods to become ready:

```bash
kubectl get pods -n argocd
kubectl rollout status deploy/argocd-server -n argocd
```

You should see the core components running: `argocd-server`,
`argocd-repo-server`, `argocd-application-controller` (a StatefulSet),
`argocd-redis`, `argocd-dex-server`, and the applicationset/notifications
controllers.

## 4. Get the initial admin password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d; echo
```

The username is `admin`. Change/rotate this password after first login — see
[09 — Securing Argo CD](09-secure-argocd-with-ingress-tls.md).

## Next

- [08 — Accessing the Argo CD UI](08-accessing-argocd-ui.md)
