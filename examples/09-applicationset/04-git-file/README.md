# 09.04 — Git File Generator

Discovers **files** in a Git repo matching a glob, parses each one (JSON/YAML), and
creates one Application per file — using the file's contents as template
parameters. This is the most flexible git generator: the *data* lives in config
files that anyone can add via a pull request.

## Files

```
04-git-file/
├── applicationset.yaml
└── envs/
    ├── qa/config.json      # { env, namespace, overlayPath }
    └── prod/config.json
```

Each `config.json`:

```json
{ "env": "qa", "namespace": "qa", "overlayPath": "examples/06-kustomize-overlays/overlays/qa" }
```

## Generates

The generator globs `envs/**/config.json` (matching both files) and produces:

- `myapp-qa`   → path/namespace taken from `envs/qa/config.json`
- `myapp-prod` → path/namespace taken from `envs/prod/config.json`

The JSON keys become parameters directly: `{{env}}`, `{{namespace}}`,
`{{overlayPath}}`.

## Try it

```bash
kubectl apply -f examples/09-applicationset/04-git-file/applicationset.yaml
kubectl get applicationset myapp-git-file -n argocd
kubectl get applications -n argocd          # myapp-qa, myapp-prod
```

## The point

Onboarding a new environment is a **single config file** in a pull request — no
change to the ApplicationSet or any Kubernetes manifest. Add
`envs/staging/config.json` and `myapp-staging` appears.
