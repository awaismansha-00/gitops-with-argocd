# 09.03 — Git Directory Generator

Discovers **directories** in a Git repo matching a path glob, and creates one
Application per matched directory. Perfect for "one app per folder" layouts.

## Generates

Globs `examples/06-kustomize-overlays/overlays/*`, which matches `qa/` and `prod/`,
producing:

- `myapp-qa`   → deploys `overlays/qa` into namespace `qa`
- `myapp-prod` → deploys `overlays/prod` into namespace `prod`

`{{path}}` is the full matched path; `{{path.basename}}` is the last segment
(`qa`, `prod`).

## Try it

```bash
kubectl apply -f examples/09-applicationset/03-git-directory/applicationset.yaml
kubectl get applicationset myapp-git-dir -n argocd
kubectl get applications -n argocd          # myapp-qa, myapp-prod
```

## The point

This is **auto-discovery**: add a new `overlays/staging/` directory in Git, and the
ApplicationSet controller creates `myapp-staging` on its own — no edit to the
ApplicationSet needed. Contrast with the [list generator](../01-list/), where you
must edit the manifest for every new environment.
