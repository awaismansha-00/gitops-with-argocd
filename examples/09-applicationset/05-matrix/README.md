# 09.05 — Matrix Generator

Combines **two child generators** and produces the **cross-product** of their
parameters, merging both into each generated Application. Use it when apps are
defined along two independent axes (e.g. environment × region, or app × cluster).

## Generates

- Child 1 — git directory over `overlays/*` → `qa`, `prod` (`{{path.basename}}`)
- Child 2 — list of regions → `us`, `eu` (`{{region}}`)

Cross-product = **4 Applications**:

| Application       | overlay (`{{path}}`) | region |
|-------------------|----------------------|--------|
| `myapp-qa-us`     | overlays/qa          | us     |
| `myapp-qa-eu`     | overlays/qa          | eu     |
| `myapp-prod-us`   | overlays/prod        | us     |
| `myapp-prod-eu`   | overlays/prod        | eu     |

## Try it

```bash
kubectl apply -f examples/09-applicationset/05-matrix/applicationset.yaml
kubectl get applicationset myapp-matrix -n argocd
kubectl get applications -n argocd          # 4 apps
```

## Notes

- A matrix takes **exactly two** child generators. To combine more axes, nest a
  matrix inside a matrix, or use the `merge` generator to combine by key.
- Parameter names must not collide between the two children — here `path.*` comes
  from the git generator and `region` from the list generator, so they merge
  cleanly.
