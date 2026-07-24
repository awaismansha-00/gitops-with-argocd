# GitOps with Argo CD

A practical guide to understanding GitOps and implementing real-world Kubernetes deployments using Argo CD.

> Status: ✅ Docs, examples, and manifests written. Diagrams/screenshots and live-cluster verification still to come.

## Introduction

This repo is a hands-on learning resource for **GitOps**: operating Kubernetes
applications where **Git is the single source of truth** and an in-cluster agent
(**Argo CD**) continuously reconciles the cluster to match the repository. It
covers the concepts, a secure Argo CD setup, application patterns, production
features, runnable examples, and troubleshooting.

## What this repo covers

- GitOps fundamentals and how it differs from traditional deployment
- Argo CD architecture, installation, and secure access (TLS, RBAC, AppProjects)
- Argo CD Applications: sources, destinations, and sync behaviour
- Production patterns: App of Apps, ApplicationSet, sync waves, resource hooks
- Kustomize and Helm with Argo CD
- The end-to-end CI + GitOps flow
- Practical, self-contained examples and a troubleshooting guide

## Architecture

_Diagram placeholder — see_ `assets/diagrams/argocd-architecture.png` _(to be added)._

## Learning roadmap

1. **Fundamentals** — `docs/01`–`docs/05`
2. **Argo CD basics** — `docs/06`–`docs/08`
3. **Secure access** — `docs/09`
4. **Applications** — `docs/10`–`docs/13`
5. **Production patterns** — `docs/14`–`docs/19`
6. **CI + GitOps flow** — `docs/20`
7. **Troubleshooting** — `docs/21`

Work through the [`examples/`](examples/) folder alongside the docs, in order.

## Tools used

- Kubernetes
- Argo CD
- Kustomize & Helm
- Git / GitHub
- (Later) GitHub Actions for CI — see the companion `ci-with-github-actions` repo

## Folder structure

```
gitops-with-argocd/
├── README.md      # this landing page
├── docs/          # concept + how-to guides (01–21)
├── examples/      # small, self-contained Argo CD demos (01–08)
├── manifests/     # reusable YAML (install, apps, ingress-tls, rbac)
├── assets/        # diagrams and screenshots
└── notes/         # scratch notes and handy commands
```

## How to use this repo

1. Read the docs in [`docs/`](docs/) in numeric order.
2. Apply the matching example under [`examples/`](examples/) to your own cluster.
3. Reuse the base YAML in [`manifests/`](manifests/) as starting points.

> Note: the manifests and examples are written to be correct by convention but have
> not yet been verified against a live cluster. Update the `repoURL` fields to your
> fork before applying. See each example's README for exact commands.

## Status / roadmap

Content is written; visual assets and live verification remain. Progress:

- [x] Fundamentals docs (01–05)
- [x] Argo CD basics + secure access (06–09)
- [x] Applications & sync (10–13)
- [x] Production patterns (14–19)
- [x] CI + GitOps flow (20)
- [x] Troubleshooting (21)
- [x] Examples (01–08)
- [x] ApplicationSet examples — 5 generators (09)
- [x] Manifests (install, apps, ingress-tls, rbac)
- [ ] Diagrams & screenshots
- [ ] Verify manifests/examples against a live cluster
