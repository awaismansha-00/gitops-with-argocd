# Example 04 — Sync Waves

Order resource creation using the `argocd.argoproj.io/sync-wave` annotation. Argo CD
applies lower waves first and waits for each wave to be Healthy before the next.

Related docs: [14 — Sync Waves](../../docs/14-sync-waves.md).

## What this example demonstrates

- Wave `0`: a ConfigMap the app depends on.
- Wave `1`: a backend Deployment + Service that consumes the ConfigMap.
- Wave `2`: a frontend Deployment created only after the backend is Healthy.

## Files

- `00-namespace-config.yaml` — ConfigMap (wave 0).
- `01-backend.yaml` — backend Deployment + Service (wave 1).
- `02-frontend.yaml` — frontend Deployment (wave 2).
- `application.yaml` — the Argo CD Application.

## Try it

```bash
kubectl apply -f examples/04-sync-waves/application.yaml
# Watch the resource tree fill in wave by wave:
argocd app get sync-waves-demo --refresh
kubectl get all -n sync-waves
```

In the Argo CD UI you'll see wave 0 sync and go Healthy, then wave 1, then wave 2.
