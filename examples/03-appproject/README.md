# Example 03 — AppProject Security Boundary

Use an `AppProject` to restrict **what** an Application may deploy, **from where**,
and **to where** — limiting blast radius.

Related docs: [12 — AppProject](../../docs/12-appproject.md),
[09 — Securing Argo CD](../../docs/09-secure-argocd-with-ingress-tls.md).

## What this example demonstrates

- A project that permits only this repo as a source.
- A project that permits only the `restricted-demo` namespace as a destination.
- A resource whitelist (Deployment, Service, ConfigMap) — anything else is denied.

## Files

- `app-project.yaml` — the restrictive `AppProject`.
- `application.yaml` — an Application bound to that project (reuses Example 01's
  nginx manifests).

## Try it

```bash
kubectl apply -f examples/03-appproject/app-project.yaml
kubectl apply -f examples/03-appproject/application.yaml
argocd app get nginx-restricted
```

## See the boundary work

- Change the Application's `destination.namespace` to `default` and re-sync — Argo CD
  **rejects** it because the project doesn't allow that destination.
- Try adding a resource kind that isn't whitelisted (e.g. a `Secret`) to the source
  path — the sync is blocked by the project.
