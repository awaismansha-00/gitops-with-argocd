# Resource Hooks

**Resource hooks** let you run resources (typically Jobs) at specific points in the
sync lifecycle — for example a database migration before the new version rolls out,
or a smoke test afterwards.

## Hook phases

Annotate a resource with `argocd.argoproj.io/hook`:

- **`PreSync`** — runs *before* the main sync. Use for migrations, backups,
  pre-flight checks.
- **`Sync`** — runs *during* the main sync, alongside normal resources.
- **`PostSync`** — runs *after* all resources are applied and Healthy. Use for
  smoke tests, notifications, cache warming.
- **`SyncFail`** — runs only if the sync fails. Use for cleanup/rollback.
- **`Skip`** — tells Argo CD not to apply the resource.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: db-migrate
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: HookSucceeded
spec:
  template:
    spec:
      restartPolicy: Never
      containers:
        - name: migrate
          image: myapp/migrations:latest
          command: ["./migrate.sh"]
```

## Hook delete policies

Control when Argo CD cleans up hook resources with
`argocd.argoproj.io/hook-delete-policy`:

- **`HookSucceeded`** — delete after the hook succeeds (common for one-shot Jobs).
- **`HookFailed`** — delete after the hook fails.
- **`BeforeHookCreation`** — delete the previous instance before creating a new one
  (default-ish behaviour; keeps only the latest).

## Hooks vs sync waves

- **Sync waves** order *ordinary* resources relative to each other.
- **Hooks** run *lifecycle* resources at Pre/Sync/Post/Fail points.
- They compose: hooks also honour `sync-wave` annotations, so you can order multiple
  PreSync hooks.

## Common use cases

- Database schema **migrations** (PreSync).
- **Backups** before a risky change (PreSync).
- **Smoke tests** / health verification (PostSync).
- **Rollback or cleanup** on failure (SyncFail).

Demonstrated in [Example 05 — Resource Hooks](../examples/05-resource-hooks/).

## Next

- [16 — App of Apps Pattern](16-app-of-apps.md)
