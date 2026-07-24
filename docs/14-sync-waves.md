# Sync Waves

By default Argo CD applies all of an application's resources together. **Sync
waves** let you impose an **order** — useful when some resources must exist and be
healthy before others are created.

## How it works

Annotate resources with `argocd.argoproj.io/sync-wave`. Argo CD groups resources by
wave and applies them in **ascending numeric order** (negative numbers run first).
It waits for each wave's resources to become **Healthy** before starting the next.

```yaml
metadata:
  annotations:
    argocd.argoproj.io/sync-wave: "1"
```

- Default wave is `0`.
- Lower (including negative) waves run earlier.
- Within a wave, Argo CD still respects Kubernetes kind ordering (namespaces, then
  CRDs, etc.).

## Example ordering

```
wave -1  Namespace, CRDs, ConfigMaps/Secrets
wave  0  Database StatefulSet + Service
wave  1  Backend Deployment (needs the DB ready)
wave  2  Frontend Deployment + Ingress
```

## Common use cases

- Create a **namespace or CRDs** before the workloads that use them.
- Bring up a **database** before the app that connects to it.
- Run a **migration hook** (see [15 — Resource Hooks](15-resource-hooks.md)) between
  waves.
- Roll out **infrastructure add-ons** before applications in an App-of-Apps.

## Waves + hooks

Hooks also participate in wave ordering, so you can, for example, run a `PreSync`
migration Job in an early wave and only then create the Deployment.

Demonstrated in [Example 04 — Sync Waves](../examples/04-sync-waves/).

## Next

- [15 — Resource Hooks](15-resource-hooks.md)
