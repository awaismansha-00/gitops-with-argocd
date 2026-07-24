# Example 06 — Kustomize Overlays

A shared **base** plus **QA** and **Prod** overlays that patch only the differences
(replica count, image tag, name prefix, labels). Argo CD auto-detects Kustomize when
a `kustomization.yaml` is present.

Related docs: [18 — Kustomize with Argo CD](../../docs/18-kustomize-with-argocd.md),
[11 — QA and Prod Applications](../../docs/11-qa-prod-applications.md).

These overlays are the source for [Example 02](../02-qa-prod-apps/) and for the
reusable Applications in [`manifests/argocd-apps/`](../../manifests/argocd-apps/).

## Layout

```
06-kustomize-overlays/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── overlays/
    ├── qa/kustomization.yaml     # 1 replica, qa- prefix
    └── prod/kustomization.yaml   # 3 replicas, prod- prefix
```

## Preview the rendered output locally

```bash
kustomize build examples/06-kustomize-overlays/overlays/qa
kustomize build examples/06-kustomize-overlays/overlays/prod
```

## Deploy via Argo CD

Point an Application's `path` at an overlay directory — see the QA/Prod Applications
in [Example 02](../02-qa-prod-apps/).
