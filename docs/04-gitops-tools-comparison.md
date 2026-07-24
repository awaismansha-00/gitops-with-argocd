# GitOps Tools Comparison

Several tools implement the GitOps model for Kubernetes. They share the same core
idea — reconcile the cluster to match Git — but differ in scope, packaging, and
ergonomics.

## The main tools

### Argo CD
- CNCF **graduated** project, maintained under the Argo project.
- Strong **web UI** visualising application/resource health and sync status.
- First-class `Application`, `AppProject`, and `ApplicationSet` CRDs.
- Rich RBAC, SSO, projects, sync waves, and resource hooks.
- Great fit when teams want visibility and a UI-driven experience.

### Flux CD
- CNCF **graduated** project (the "GitOps Toolkit").
- API/controller-first, **no bundled UI** (UIs exist separately, e.g. Weave GitOps).
- Composable controllers (source, kustomize, helm, image automation).
- Built-in **image update automation** out of the box.
- Great fit for teams that prefer a lightweight, CRD-driven, composable approach.

### Fleet (Rancher)
- Built by SUSE/Rancher for **GitOps at scale across many clusters**.
- Strong multi-cluster fleet management, integrated with Rancher.

### Weave GitOps
- Built on top of Flux, adding a UI and enterprise features.

### Config Sync (Google)
- Part of Anthos Config Management; GitOps for GKE/Anthos fleets.

## Quick comparison

| Tool          | UI             | Multi-cluster | Image automation  | Governance CRDs |
| ------------- | -------------- | ------------- | ----------------- | --------------- |
| Argo CD       | Built-in, rich | Yes           | Via Image Updater | AppProject      |
| Flux CD       | External       | Yes           | Built-in          | Via tenancy     |
| Fleet         | Via Rancher    | Strong        | Limited           | GitRepo/Bundle  |
| Weave GitOps  | Yes (on Flux)  | Yes           | Built-in (Flux)   | Via Flux        |
| Config Sync   | Via Anthos     | Yes (fleets)  | Limited           | RootSync        |

## Argo CD vs Flux in one line

- **Argo CD**: application-centric, UI-first, great visibility → chosen for this repo.
- **Flux**: toolkit-centric, controller-first, great composability.

## Next

- [05 — Why Argo CD?](05-why-argocd.md)
