# Accessing the Argo CD UI

By default the `argocd-server` service is `ClusterIP` — reachable only inside the
cluster. There are three common ways to reach it, in increasing order of
production-readiness.

## Retrieving the admin password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d; echo
```

Username: `admin`.

## Option 1 — port-forward (local / dev)

Quickest for local testing; no cluster networking changes needed:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Then open https://localhost:8080 (accept the self-signed cert warning). You can
also log in with the CLI:

```bash
argocd login localhost:8080 --username admin --password <password> --insecure
```

## Option 2 — LoadBalancer (cloud)

Expose the service via a cloud load balancer:

```bash
kubectl patch svc argocd-server -n argocd \
  -p '{"spec": {"type": "LoadBalancer"}}'
kubectl get svc argocd-server -n argocd   # note the EXTERNAL-IP
```

Good for quick cloud access, but it exposes Argo CD directly and typically without a
proper certificate/hostname. Prefer Ingress for anything shared.

## Option 3 — Ingress (production)

Front Argo CD with an Ingress controller so you get a real hostname and TLS
termination. See [09 — Securing Argo CD with Ingress + TLS](09-secure-argocd-with-ingress-tls.md)
and [`manifests/ingress-tls/argocd-ingress.yaml`](../manifests/ingress-tls/argocd-ingress.yaml).

> Note: `argocd-server` serves both gRPC and HTTP. When placing it behind an
> Ingress you either terminate TLS at the Ingress and run the server in
> `--insecure` mode, or pass TLS through. The example manifest documents the
> common patterns.

## Next

- [09 — Securing Argo CD with Ingress + TLS](09-secure-argocd-with-ingress-tls.md)
