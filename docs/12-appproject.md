# AppProject

An **AppProject** is Argo CD's governance and security boundary. Every
`Application` belongs to exactly one project. The `default` project permits
everything; real deployments define scoped projects to enforce least privilege.

## What an AppProject restricts

- **Which source repositories** applications may deploy from (`sourceRepos`).
- **Which destinations** (cluster + namespace) they may deploy to
  (`destinations`).
- **Which resource kinds** they may create — cluster-scoped
  (`clusterResourceWhitelist`/`Blacklist`) and namespace-scoped
  (`namespaceResourceWhitelist`/`Blacklist`).
- **Who can do what** within the project, via project **roles** and RBAC.
- Optional **sync windows** to restrict *when* syncs may happen (see
  [14 — Sync Waves](14-sync-waves.md) is separate; sync windows are a project
  feature).

## Example

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: demo
  namespace: argocd
spec:
  description: Demo project for the gitops-with-argocd repo
  sourceRepos:
    - https://github.com/DexterCage97/gitops-with-argocd.git
  destinations:
    - server: https://kubernetes.default.svc
      namespace: qa
    - server: https://kubernetes.default.svc
      namespace: prod
  clusterResourceWhitelist:
    - group: ''
      kind: Namespace
  namespaceResourceWhitelist:
    - group: '*'
      kind: '*'
  namespaceResourceBlacklist:
    - group: ''
      kind: ResourceQuota
```

See [`manifests/argocd-apps/app-project.yaml`](../manifests/argocd-apps/app-project.yaml).

## Why this matters

Without a scoped project, any Application (or a compromised/misconfigured one) could
deploy anything, anywhere, from any repo. An AppProject **limits the blast radius**:
a project scoped to the `qa` and `prod` namespaces and a single repo can't be
tricked into deploying a `ClusterRoleBinding` from an attacker's repo.

This ties directly to [09 — Securing Argo CD](09-secure-argocd-with-ingress-tls.md)
and is demonstrated in [Example 03 — AppProject](../examples/03-appproject/).

## Next

- [13 — Sync Policies](13-sync-policies.md)
