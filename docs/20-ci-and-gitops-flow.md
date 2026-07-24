# CI and GitOps Flow

GitOps replaces the **CD** half of the pipeline; **CI** still builds and validates
your code. This doc explains how the two halves connect end to end. The CI details
themselves belong to the companion repo, `ci-with-github-actions` (future).

## The full flow

```
Developer pushes code
        │
        ▼
GitHub Actions runs tests / lint
        │
        ▼
GitHub Actions builds a Docker image
        │
        ▼
Security scans run (SAST, image scan)
        │
        ▼
Image is pushed to a registry (tagged, e.g. sha or version)
        │
        ▼
Manifest image tag is updated in Git   ◄── the handoff point
        │
        ▼
Argo CD detects the Git change (webhook or poll)
        │
        ▼
Argo CD syncs and deploys to Kubernetes
```

## The handoff point

Everything above "manifest image tag is updated in Git" is **CI** (the future
`ci-with-github-actions` repo). Everything below is **CD/GitOps** (this repo). The
two connect at a single, auditable action: **committing a new image tag to the
GitOps repo**.

## Two ways to update the image tag

1. **CI writes the tag (push-to-Git)** — after pushing the image, the CI job commits
   the new tag into the manifests (e.g. edits a Kustomize `images:` entry or a Helm
   `values.yaml`) and pushes. Simple, explicit, fully auditable in Git history. See
   [30-image-tag-update-strategy] concepts summarised below.
2. **Argo CD Image Updater** — a separate component watches the registry for new
   tags matching a policy and writes the update back to Git (or annotates the app).
   Less pipeline glue, but another component to run.

## Image tag update strategy (in brief)

- Prefer **immutable, unique tags** (git SHA or semver) over `latest` so each deploy
  is reproducible and revertible.
- Update the tag **in one place** per environment (a Kustomize overlay `images:`
  entry or a Helm value) so the diff is small and reviewable.
- Promotion (QA → Prod) is just changing which tag Prod references — a PR, not a
  pipeline rerun. See [11 — QA and Prod Applications](11-qa-prod-applications.md).

## Why this separation is good

- **Least privilege** — CI never needs cluster credentials; it only writes to Git.
- **Auditable** — every deployment corresponds to a Git commit.
- **Revertible** — roll back by reverting the tag commit; Argo CD redeploys.

## Companion repo

The CI specifics (test matrices, Docker build, OIDC to the registry, image
scanning) will live in **`ci-with-github-actions`**. This repo focuses on the CD/
GitOps side and simply consumes the image tags CI produces.

## Next

- [21 — Troubleshooting](21-troubleshooting.md)
