# QA and Prod Applications

A common real-world need is running the **same app in multiple environments** — for
example QA and Production — with small, controlled differences. In Argo CD you model
each environment as its own `Application`, pointing at an environment-specific path
or values.

## One repo, two Applications

```yaml
# qa-application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-qa
  namespace: argocd
spec:
  project: demo
  source:
    repoURL: https://github.com/awaismansha-00/gitops-with-argocd.git
    targetRevision: main
    path: examples/06-kustomize-overlays/overlays/qa
  destination:
    server: https://kubernetes.default.svc
    namespace: qa
  syncPolicy:
    automated: { selfHeal: true, prune: true }
    syncOptions: [CreateNamespace=true]
```

```yaml
# prod-application.yaml — differs only in name, path, and namespace
metadata:
  name: myapp-prod
spec:
  source:
    path: examples/06-kustomize-overlays/overlays/prod
  destination:
    namespace: prod
```

See [`manifests/argocd-apps/`](../manifests/argocd-apps/) for reusable versions.

## Managing environment differences

Prefer a mechanism that keeps a shared **base** and expresses only the *deltas* per
environment:

- **Kustomize overlays** — a `base/` plus `overlays/qa` and `overlays/prod` that
  patch replica counts, image tags, resources, hostnames, etc. See
  [18 — Kustomize with Argo CD](18-kustomize-with-argocd.md).
- **Helm values** — one chart with `values-qa.yaml` / `values-prod.yaml`. See
  [19 — Helm with Argo CD](19-helm-with-argocd.md).

## Sensible per-environment policy

- **QA** — full automation: `automated`, `selfHeal`, `prune` on. Fast feedback.
- **Prod** — often **manual sync** (or automated with change control), so a human
  approves promotions. You can still keep `selfHeal` to correct drift.

## Promotion

To promote QA → Prod you change what Prod points at (a new image tag or a merged
overlay change) **in Git**. Argo CD deploys it. This keeps promotions auditable and
revertible. See [20 — CI and GitOps Flow](20-ci-and-gitops-flow.md).

## Next

- [12 — AppProject](12-appproject.md)
