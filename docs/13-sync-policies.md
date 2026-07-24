# Sync Policies

A sync policy controls **how** Argo CD reconciles the desired state (Git) with the
live state (cluster). It is the practical heart of GitOps behaviour.

## Sync status vs health status

Argo CD reports two independent statuses per application:

- **Sync status** — does live match Git?
  - `Synced` — live state matches the desired state.
  - `OutOfSync` — they differ (a change in Git not yet applied, or drift in the
    cluster).
- **Health status** — is the workload actually healthy?
  - `Healthy`, `Progressing`, `Degraded`, `Missing`, `Suspended`, `Unknown`.

An app can be `Synced` but `Degraded` (deployed correctly but crashing), or
`OutOfSync` but `Healthy` (running fine, but not what Git says). You want
`Synced + Healthy`.

## Manual vs automated sync

- **Manual** (no `syncPolicy.automated`) — Argo CD shows `OutOfSync` and waits for a
  human to click **Sync** (or run `argocd app sync`). Good for production change
  control.
- **Automated** — Argo CD applies changes automatically whenever Git changes:

  ```yaml
  syncPolicy:
    automated:
      selfHeal: true
      prune: true
  ```

## `selfHeal`

With `selfHeal: true`, Argo CD reverts **drift in the cluster** — if someone runs
`kubectl edit` or `kubectl scale` by hand, Argo CD changes it back to match Git.
Without it, manual changes are reported as `OutOfSync` but left in place.

## `prune`

With `prune: true`, when you **delete a resource from Git**, Argo CD deletes it from
the cluster on the next sync. Without prune, removed resources are left orphaned
(shown as needing prune). Prune is powerful — combine with care and, for safety,
`PruneLast=true` or resource-level `Prune=false` annotations where needed.

## Useful sync options

```yaml
syncPolicy:
  syncOptions:
    - CreateNamespace=true       # create the destination namespace
    - ApplyOutOfSyncOnly=true    # only apply resources that changed
    - PruneLast=true             # prune after other resources are healthy
    - ServerSideApply=true       # use server-side apply
```

## Trying it out

- Change a replica count in Git → app goes `OutOfSync` → syncs to the new count.
- `kubectl scale` a Deployment manually with `selfHeal: true` → Argo CD reverts it.
- Delete a manifest from Git with `prune: true` → the object is removed.

Demonstrated in [Example 01 — Basic Nginx App](../examples/01-basic-nginx-app/).

## Next

- [14 — Sync Waves](14-sync-waves.md)
