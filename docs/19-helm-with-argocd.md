# Helm with Argo CD

Argo CD can deploy **Helm charts** — from a Git repo, a Helm repository, or an OCI
registry. Importantly, Argo CD **renders** the chart (`helm template`) and applies
the result itself; it does **not** use Tiller or track Helm releases in the usual
way. This keeps everything reconciled against Git.

## Chart from a Git repo

If your chart lives in the repo, point `path` at the chart directory and pass values:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-helm
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/awaismansha-00/gitops-with-argocd.git
    targetRevision: main
    path: examples/07-helm-app/chart
    helm:
      valueFiles:
        - values.yaml
      parameters:
        - name: image.tag
          value: "1.27.1"
  destination:
    server: https://kubernetes.default.svc
    namespace: helm-demo
  syncPolicy:
    automated: { selfHeal: true, prune: true }
    syncOptions: [CreateNamespace=true]
```

## Chart from a Helm/OCI repository

```yaml
source:
  repoURL: https://charts.bitnami.com/bitnami   # or oci://registry/...
  chart: nginx
  targetRevision: 15.14.0        # the chart version
  helm:
    values: |
      replicaCount: 2
```

## Supplying values

- **`valueFiles`** — reference one or more values files in the repo
  (e.g. `values-qa.yaml`, `values-prod.yaml`) — the Helm equivalent of Kustomize
  overlays for QA/Prod.
- **`parameters`** — override individual values (like `--set`), e.g. the image tag.
  This is a common hook point for CI to bump the deployed version.
- **`values`** — an inline values block.

## Notes and gotchas

- Argo CD renders with `helm template`, so **Helm hooks** are mapped onto Argo CD's
  hook model where possible; prefer Argo CD [resource hooks](15-resource-hooks.md)
  for sync-lifecycle tasks.
- Because Argo CD tracks Git, avoid `helm install`/`upgrade` out-of-band — that
  creates drift.
- For private chart repos, register credentials just like private Git repos
  (see [09 — Securing Argo CD](09-secure-argocd-with-ingress-tls.md)).

Demonstrated in [Example 07 — Helm Application](../examples/07-helm-app/).

## Next

- [20 — CI and GitOps Flow](20-ci-and-gitops-flow.md)
