# App of Apps Pattern

The **App of Apps** pattern uses a single *parent* Application whose Git path
contains **other Application manifests**. Syncing the parent creates/updates all the
children. It's the classic way to bootstrap and manage many applications from one
entry point.

## How it works

```
parent Application  ──► path: apps/   (a folder full of Application YAML)
        │
        ├── child: nginx  Application
        ├── child: qa     Application
        └── child: prod   Application
```

The parent's source path contains child `Application` resources. Argo CD applies
them like any other manifest; each child then deploys its own workloads.

## Parent Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: bootstrap
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/DexterCage97/gitops-with-argocd.git
    targetRevision: main
    path: examples/08-app-of-apps/apps      # folder of child Applications
  destination:
    server: https://kubernetes.default.svc
    namespace: argocd
  syncPolicy:
    automated: { selfHeal: true, prune: true }
```

Each file in `apps/` is a child `Application` pointing at its own path.

## When to use it

- **Bootstrapping a cluster** — one parent brings up add-ons and apps in order
  (combine with [sync waves](14-sync-waves.md)).
- **Grouping** a set of related applications you want to manage/sync together.
- **Onboarding** — adding an app is a commit that drops one `Application` file into
  the parent's folder.

## App of Apps vs ApplicationSet

- **App of Apps** — you *hand-write* each child Application (static, explicit).
- **ApplicationSet** — children are *generated* from a template + generator
  (dynamic, DRY). Prefer ApplicationSet when children are near-identical across many
  environments/clusters. See [17 — ApplicationSet](17-applicationset.md).

Demonstrated in [Example 08 — App of Apps](../examples/08-app-of-apps/).

## Next

- [17 — ApplicationSet](17-applicationset.md)
