# Argo CD Architecture

Argo CD is a set of Kubernetes controllers and services that together implement
pull-based GitOps. Understanding the pieces makes troubleshooting much easier.

## High-level flow

```
        Git repo (desired state)
              │  clone / fetch
              ▼
        ┌───────────┐        ┌──────────────────────┐
        │ Repo      │ render │ Application          │
        │ Server    ├───────►│ Controller           │──► compares desired vs live
        └───────────┘        └──────────┬───────────┘        and applies changes
              ▲                          │
              │                          ▼
        ┌───────────┐             Kubernetes API (live state)
        │ API Server│◄── UI / CLI / API
        └─────┬─────┘
              │
           Redis (cache)
```

## Core components

### API server (`argocd-server`)
The gRPC/REST front door. It serves the **Web UI**, backs the **CLI**, handles
authentication/authorization (RBAC, SSO/Dex), and exposes application management
operations. This is the component you expose (securely) to users.

### Repo server (`argocd-repo-server`)
Clones Git repositories and **renders the desired manifests** — running Helm,
Kustomize, or plain YAML — and returns the generated manifests. It is stateless and
caches results.

### Application controller (`argocd-application-controller`)
The reconciliation engine. It continuously **compares the desired state** (from the
repo server) against the **live state** (from the Kubernetes API), reports
`Sync`/`Health` status, and applies changes to converge them (respecting sync
policies, waves, and hooks).

### Dex server (`argocd-dex-server`)
Optional. Provides **SSO** by federating to external identity providers (OIDC,
SAML, LDAP, GitHub, etc.). Not needed if you use Argo CD's built-in local users or
an external OIDC provider directly.

### Redis (`argocd-redis`)
An in-memory **cache** used by the other components to avoid repeatedly hitting Git
and the Kubernetes API. It holds ephemeral data only.

### ApplicationSet controller (`argocd-applicationset-controller`)
Generates `Application` resources from generators (list, cluster, git, matrix…).
See [17 — ApplicationSet](17-applicationset.md).

### Notifications controller (optional)
Sends alerts (Slack, email, webhooks) on sync/health events.

## Interfaces

- **Argo CD UI** — served by the API server; a live resource tree with sync/health.
- **Argo CD CLI (`argocd`)** — talks to the API server for scripting and automation.
- **Declarative CRDs** — `Application`, `AppProject`, `ApplicationSet` applied with
  `kubectl`; this is the GitOps-native way (App of Apps manages these too).

## Next

- [07 — Installing Argo CD](07-installing-argocd.md)
