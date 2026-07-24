# Example 05 — Resource Hooks

Run Jobs at specific points in the sync lifecycle using
`argocd.argoproj.io/hook` — a PreSync migration and a PostSync smoke test.

Related docs: [15 — Resource Hooks](../../docs/15-resource-hooks.md).

## What this example demonstrates

- **PreSync** hook (`db-migrate`) runs before the workload is applied.
- **Sync** phase applies the main Deployment.
- **PostSync** hook (`smoke-test`) runs after everything is Healthy.
- Hook Jobs are auto-deleted on success (`hook-delete-policy: HookSucceeded`).

## Files

- `presync-migrate-job.yaml` — PreSync hook Job.
- `deployment.yaml` — the main workload.
- `postsync-smoketest-job.yaml` — PostSync hook Job.
- `application.yaml` — the Argo CD Application.

## Try it

```bash
kubectl apply -f examples/05-resource-hooks/application.yaml
argocd app get resource-hooks-demo --refresh
# During sync you'll see db-migrate run first, then the Deployment, then smoke-test.
kubectl get jobs,pods -n resource-hooks
```
