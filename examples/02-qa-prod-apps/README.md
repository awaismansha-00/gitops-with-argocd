# Example 02 — QA and Prod Applications

Two Argo CD Applications sourced from the **same repository** but pointing at
different environment overlays and namespaces. This models promoting the same app
through environments with small, controlled differences.

Related docs: [11 — QA and Prod Applications](../../docs/11-qa-prod-applications.md).

The environment differences (replica count, image tag) live in the Kustomize
overlays in [Example 06](../06-kustomize-overlays/) — this example reuses them so
the environment deltas live in exactly one place.

## What this example demonstrates

- One repo driving multiple environments via separate Applications.
- Different sync policies per environment (QA auto-prunes; Prod does not).
- How promotion becomes a Git change to what Prod references.

## Files

- `qa-application.yaml` — deploys the QA overlay into the `qa` namespace.
- `prod-application.yaml` — deploys the Prod overlay into the `prod` namespace.

## Try it

```bash
kubectl apply -f examples/02-qa-prod-apps/qa-application.yaml
kubectl apply -f examples/02-qa-prod-apps/prod-application.yaml
argocd app list
kubectl get deploy -n qa -o wide
kubectl get deploy -n prod -o wide   # note the different replica count / image tag
```
