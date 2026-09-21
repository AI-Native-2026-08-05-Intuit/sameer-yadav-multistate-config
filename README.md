# sameer-yadav-multistate-config

GitOps config repo for `multistate-api`. Argo CD (running in the
`multistate-dev` k3d cluster) watches this repo and pulls desired state in
— see `AI-Native-2026-08-05-Intuit/multistate-tax`'s `GITOPS.md` for the
full reconcile loop, and `argocd/`, `argocd-system/` here for the Argo CD
control-plane resources.

## Layout

```
base/                     W5D3 manifests, environment-agnostic (+ W6D5 worker/KEDA)
overlays/{dev,staging,prod}/   Kustomize overlays (namespace, replicas, image tag, config)
overlays/dev/50-hpa.yaml  W6D5 SLO-derived HPA + PDB (dev only, see file header)
argocd/projects/          AppProject (allow-lists + RBAC)
argocd/applications/      Task-1 anchor Application (dev)
argocd/applicationsets/   Matrix generator -> one Application per env
argocd-system/            Argo CD control-plane config (notifications, cluster registration)
prometheus-adapter-values.yaml   W6D5 Helm values, see PROMETHEUS-ADAPTER.md
```

Do not hand-edit the `newTag:` in `overlays/*/kustomization.yaml` — the
app repo's `_bump-config.yml` workflow rewrites it on every green `main`
build.
