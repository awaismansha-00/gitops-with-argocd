# Push-based vs Pull-based Deployment

"Push" and "pull" describe **the direction credentials and control flow travel**
when a change reaches the cluster. This is the core mechanical difference between
traditional CD and GitOps.

## Push-based

An external system (a CI/CD pipeline) is triggered by a change and **pushes** the
new state into the cluster.

```
Pipeline ──(has cluster credentials)──► deployment commands ──► Cluster
```

Pros:

- Simple and familiar; works with any CI system.
- Immediate — the pipeline applies changes as soon as it runs.

Cons:

- The pipeline needs **cluster credentials**, widening the attack surface.
- No continuous reconciliation: drift between runs is invisible.
- The cluster's real state can diverge from Git without anyone noticing.

## Pull-based

An agent **inside** the cluster watches the Git repository and **pulls** the
desired state in, applying it and continuously reconciling.

```
Cluster ◄──(agent pulls)── Git repository
   │
   └─ GitOps agent reconciles live state to match Git, on a loop
```

Pros:

- **Credentials stay in the cluster** — external systems never get cluster access.
- **Continuous reconciliation** — drift is detected and can be auto-healed.
- The cluster can be rebuilt from Git at any time.

Cons:

- Requires running and operating an in-cluster GitOps agent.
- Slightly less immediate — the agent syncs on a polling interval or webhook.

## Why GitOps defaults to pull-based

A pull-based GitOps agent runs as a controller inside the target environment (or a management cluster).
It clones the Git repo, compares rendered manifests against live objects, and reconciles — no outside
system is ever handed cluster credentials. This is what makes pull-based deployment the standard model
for GitOps.

## Next

- [04 — GitOps Tools Comparison](04-gitops-tools-comparison.md)
- [05 — Why Argo CD?](05-why-argocd.md)
