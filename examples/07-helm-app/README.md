# Example 07 — Helm Application

Deploy a local Helm chart through Argo CD with custom values. Argo CD renders the
chart (`helm template`) and applies the result — no Tiller, no out-of-band
`helm install`.

Related docs: [19 — Helm with Argo CD](../../docs/19-helm-with-argocd.md).

## Layout

```
07-helm-app/
├── application.yaml          # the Argo CD Application
└── chart/
    ├── Chart.yaml
    ├── values.yaml           # replicaCount, image, service
    └── templates/
        ├── deployment.yaml
        └── service.yaml
```

## Preview the rendered output locally

```bash
helm template myapp examples/07-helm-app/chart
```

## Deploy via Argo CD

```bash
kubectl apply -f examples/07-helm-app/application.yaml
argocd app get helm-app
kubectl get all -n helm-demo
```

## Change the deployed version

Edit the `image.tag` Helm parameter in `application.yaml` (or `values.yaml`), commit
and push — Argo CD re-renders and rolls out the new tag. This is the hook point CI
uses to promote a new image. See
[20 — CI and GitOps Flow](../../docs/20-ci-and-gitops-flow.md).
