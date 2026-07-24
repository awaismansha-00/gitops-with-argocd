# Example 01 — Basic Nginx Application

A minimal Argo CD Application that deploys a plain Nginx workload from Git. This is
the "hello world" of Argo CD — start here.

Related docs: [10 — The Argo CD Application](../../docs/10-argocd-application.md),
[13 — Sync Policies](../../docs/13-sync-policies.md).

## What this example demonstrates

- Defining an `Application` that points at a repo path.
- Auto-sync with `selfHeal` and `prune`.
- Creating the destination namespace automatically.
- Seeing `Synced` / `Healthy` status in the Argo CD UI.

## Files

- `deployment.yaml` — an Nginx Deployment (2 replicas).
- `service.yaml` — a ClusterIP Service.
- `application.yaml` — the Argo CD Application that deploys the above.

## Try it

```bash
# 1. Register the application with Argo CD
kubectl apply -f examples/01-basic-nginx-app/application.yaml

# 2. Watch it sync
argocd app get nginx-app
kubectl get all -n nginx
```

## Experiments (see GitOps in action)

- **Self-heal:** scale the deployment by hand and watch Argo CD revert it.
  ```bash
  kubectl scale deploy/nginx -n nginx --replicas=5   # will be reverted to 2
  ```
- **Sync on change:** edit `replicas` in `deployment.yaml`, commit and push — the
  app goes `OutOfSync` then syncs to the new value.
- **Prune:** delete `service.yaml` from Git, commit and push — Argo CD removes the
  Service from the cluster.
