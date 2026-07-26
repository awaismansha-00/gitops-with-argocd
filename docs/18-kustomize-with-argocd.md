# Kustomize with Argo CD

**Kustomize** builds Kubernetes manifests from a **base** plus environment-specific
**overlays**, without templating. Argo CD detects Kustomize automatically when a
`kustomization.yaml` is present at the Application's `path`.

## Base + overlays layout

```
kustomize-app/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
└── overlays/
    ├── qa/
    │   └── kustomization.yaml     # patches base for QA
    └── prod/
        └── kustomization.yaml     # patches base for Prod
```

`base/kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml
```

`overlays/prod/kustomization.yaml`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base
replicas:
  - name: myapp
    count: 3
images:
  - name: nginx
    newTag: 1.27.1
```

## Pointing an Application at an overlay

Set the Application's `path` to the overlay directory:

```yaml
spec:
  source:
    repoURL: https://github.com/awaismansha-00/gitops-with-argocd.git
    targetRevision: main
    path: examples/06-kustomize-overlays/overlays/prod
```

Argo CD runs `kustomize build` on that path and deploys the result. No extra
configuration is needed for the common case.

## Kustomize options in Argo CD

You can override behaviour via `spec.source.kustomize`, e.g. add a `namePrefix`,
`images`, `commonLabels`, or `namespace`:

```yaml
source:
  kustomize:
    namePrefix: prod-
    images:
      - nginx:1.27.1
```

## Why Kustomize for environments

- No templating language — plain YAML patches, easy to review in PRs.
- A single **base** avoids duplication; overlays express only the deltas.
- Maps cleanly onto QA/Prod Applications — see
  [11 — QA and Prod Applications](11-qa-prod-applications.md).

Demonstrated in [Example 06 — Kustomize Overlays](../examples/06-kustomize-overlays/).

## Next

- [19 — Helm with Argo CD](19-helm-with-argocd.md)
