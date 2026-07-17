# gateflow-gitops

FluxCD config repo for GateFlow (layer 4 — workloads). The AKS Flux extension
(installed by `infra/platform`) watches branch `main` of this repo; the dev
cluster reconciles `clusters/dev/`.

```
clusters/
  dev/            <- dev cluster entry point (watched by Flux)
    kustomization.yaml
    namespaces.yaml
```

Planned layout as workloads arrive:

```
clusters/<env>/   <- per-cluster entry points (thin, reference the dirs below)
infrastructure/   <- gateway HelmRelease, Redis, in-cluster Postgres (dev)
apps/             <- users-svc, orders-svc (base + per-env overlays)
```

Rules:
- PR-only to `main` — merging IS deploying.
- No secrets in this repo, ever (Key Vault + Workload Identity).
- Multi-tenancy is enforced on the cluster: no cross-namespace refs; tenant
  Kustomizations must set `serviceAccountName` to a namespace-scoped SA.

Publish target: https://github.com/Achootrain/gateflow-gitops (public).
