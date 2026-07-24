# Why Argo CD?

This repo standardises on **Argo CD** as the CD engine. Here is the reasoning.

## Reasons to choose Argo CD

- **Application-centric model** — the `Application` CRD maps cleanly onto "this repo
  path deploys to this cluster/namespace," which is easy to reason about.
- **Excellent UI** — a live, visual tree of every resource with sync and health
  status makes debugging and demos dramatically easier for learners.
- **Governance built in** — `AppProject` gives you a real security boundary
  (allowed repos, destinations, and resource kinds); RBAC and SSO integrate cleanly.
- **Scales with patterns** — App of Apps and `ApplicationSet` let you manage tens or
  hundreds of applications and clusters declaratively.
- **Flexible sources** — plain manifests, Kustomize, Helm, and jsonnet are all
  supported from the same Application model.
- **Mature and widely adopted** — a CNCF graduated project with a large community,
  lots of documentation, and strong ecosystem tooling.

## What makes it a good teaching tool

- The UI shows *exactly* what "OutOfSync," "self-heal," "prune," and "degraded"
  mean, turning abstract GitOps concepts into things you can see.
- Concepts build up cleanly: Application → sync policies → AppProject → App of Apps
  → ApplicationSet, which is the roadmap this repo follows.

## When another tool might fit better

- You want **image update automation as a first-class, built-in feature** with no
  extra components → Flux is a natural fit.
- You are **all-in on Rancher** and need large-scale fleet management → Fleet.
- You are running **GKE/Anthos fleets** and want Google-native tooling → Config Sync.

For a hands-on GitOps learning repo that values visibility and clear mental models,
Argo CD is the strongest choice.

## Next

- [06 — Argo CD Architecture](06-argocd-architecture.md)
