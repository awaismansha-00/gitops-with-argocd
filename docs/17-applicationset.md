# ApplicationSet

An **ApplicationSet** generates many `Application` resources from a single template
plus a **generator**. It's the DRY, scalable answer to "the same app across many
environments/clusters/teams."

## Anatomy

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: myapp-envs
  namespace: argocd
spec:
  generators:
    - list:
        elements:
          - env: qa
            namespace: qa
          - env: prod
            namespace: prod
  template:
    metadata:
      name: 'myapp-{{env}}'
    spec:
      project: demo
      source:
        repoURL: https://github.com/awaismansha-00/gitops-with-argocd.git
        targetRevision: main
        path: 'examples/06-kustomize-overlays/overlays/{{env}}'
      destination:
        server: https://kubernetes.default.svc
        namespace: '{{namespace}}'
      syncPolicy:
        automated: { selfHeal: true, prune: true }
        syncOptions: [CreateNamespace=true]
```

The `{{...}}` placeholders are filled from each generator element, producing one
Application per element (here: `myapp-qa` and `myapp-prod`).

## Generators

- **List** — a static list of key/value elements (shown above).
  → [Example 09.01](../examples/09-applicationset/01-list/)
- **Cluster** — one Application per registered cluster (great for fleet rollouts).
  → [Example 09.02](../examples/09-applicationset/02-cluster/)
- **Git — directory** — one Application per directory matched in a repo
  (auto-discover apps). → [Example 09.03](../examples/09-applicationset/03-git-directory/)
- **Git — file** — one Application per config file matched in a repo, using the
  file's contents as parameters. → [Example 09.04](../examples/09-applicationset/04-git-file/)
- **Matrix / Merge** — combine generators (e.g. every app × every cluster).
  → [Example 09.05](../examples/09-applicationset/05-matrix/)
- **SCM Provider / Pull Request** — generate from GitHub/GitLab repos or open PRs.

## Templating

Any field in the `template` can reference generator parameters with `{{ }}`, so you
can vary name, path, namespace, cluster, values files, etc.

## When to use it

- The **same workload across many clusters** (cluster generator).
- **Per-team or per-tenant** apps discovered from a repo (git/SCM generator).
- **Preview environments** per pull request (PR generator).

Prefer ApplicationSet over App of Apps when the children are near-identical and
numerous; use App of Apps when each child is bespoke.

All five generators above are demonstrated, one per subfolder, in
[Example 09 — ApplicationSet](../examples/09-applicationset/).

For a full hub-and-spoke rollout where Argo CD runs in one cluster and targets
registered remote clusters, see
[22 — Multi-Cluster Hub-Spoke Model](22-multicluster-hub-spoke-model.md) and
[Example 10](../examples/10-multicluster-hub/).

## Next

- [18 — Kustomize with Argo CD](18-kustomize-with-argocd.md)
