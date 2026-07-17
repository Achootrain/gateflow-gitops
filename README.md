# gateflow-gitops

FluxCD config repo for GateFlow (layer 4 — workloads). The AKS Flux extension
(installed by `infra/platform`) watches branch `main` of this repo; the dev
cluster reconciles `clusters/dev/`.

```
clusters/               <- per-cluster entry points; ONLY path Azure Flux reconciles
  dev/                  <- namespaces, flux-applier SA, Kustomization CRs below
  production/           <- placeholder until a prod cluster exists
infrastructure/         <- platform team: cluster-wide services (cluster-admin)
  ingress/              <- Kong DB-less + declarative config + rate-limit Redis
  monitoring/           <- kube-prometheus-stack, Loki, Promtail
  security-policies/    <- day-2: netpols, admission policies
tenants/                <- one dir per tenant = onboarding record
  team-chat/            <- namespace, flux-tenant SA + Role, sync (impersonated)
    workloads/          <- tenant-owned subtree (chat-svc) — applied AS flux-tenant
```

Reconcile order: cluster entry (Azure-managed, privileged) → `infrastructure`
→ `tenants` (Flux Kustomization CRs with dependsOn). Tenant workloads apply
under the namespace-scoped `flux-tenant` SA — cluster-scoped resources in a
tenant tree fail with `forbidden`, by design.

Rules:
- PR-only to `main` — merging IS deploying.
- No secrets in this repo, ever (Key Vault + Workload Identity).
- Multi-tenancy is enforced on the cluster: no cross-namespace refs; tenant
  Kustomizations must set `serviceAccountName` to a namespace-scoped SA.

## Current dev layout

The platform Flux configuration reconciles `clusters/dev`, then
`infrastructure/dev`, then `apps/dev`. Each directory is independently
kustomize-buildable. Workload images must support `linux/arm64`; dev nodes are
ARM64 only.

Publish target: https://github.com/Achootrain/gateflow-gitops (public).
