# Example 08 — App of Apps

A single parent Application whose Git path contains **other Application manifests**.
Syncing the parent creates and manages all the children — the classic way to
bootstrap a cluster or manage a group of apps together.

Related docs: [16 — App of Apps Pattern](../../docs/16-app-of-apps.md),
[17 — ApplicationSet](../../docs/17-applicationset.md) (the generated alternative).

## Layout

```
08-app-of-apps/
├── root-app.yaml             # the parent Application
└── apps/                     # a folder of child Applications
    ├── nginx.yaml            # -> examples/01-basic-nginx-app
    └── kustomize-qa.yaml     # -> examples/06-kustomize-overlays/overlays/qa
```

## Try it

```bash
# Apply only the parent — it pulls in the children automatically.
kubectl apply -f examples/08-app-of-apps/root-app.yaml

argocd app list         # bootstrap + child-nginx + child-kustomize-qa
argocd app get bootstrap
```

## Add another app

Drop a new child `Application` file into `apps/`, commit and push. The parent syncs
and Argo CD creates it — onboarding an app becomes a one-file pull request.
