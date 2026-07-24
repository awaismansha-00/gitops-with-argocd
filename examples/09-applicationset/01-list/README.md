# 09.01 — List Generator

The simplest generator: you spell out each parameter set as an element in a static
`list`. One Application is created per element.

## Generates

- `myapp-qa`  → `overlays/qa` into namespace `qa`
- `myapp-prod` → `overlays/prod` into namespace `prod`

## Try it

```bash
kubectl apply -f examples/09-applicationset/01-list/applicationset.yaml
kubectl get applicationset myapp-list -n argocd
kubectl get applications -n argocd          # myapp-qa, myapp-prod
```

## Note

Add another element (e.g. `env: staging`) and re-apply — a new Application appears
automatically. The list generator is best when the set is small and rarely changes;
for anything derived from Git or clusters, prefer the git/cluster generators.
