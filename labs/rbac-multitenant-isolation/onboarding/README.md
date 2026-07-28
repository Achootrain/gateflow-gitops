# Learner-owned tenant onboarding

Create the following files in this directory and add them to
`kustomization.yaml`:

- `finance.yaml`
- `support.yaml`

Each tenant file must provide:

- a `flux-tenant` ServiceAccount;
- a least-privilege Role and RoleBinding;
- a ResourceQuota and LimitRange; and
- a namespaced Flux Kustomization which uses `flux-tenant`, references the
  same-namespace `tenant-source`, and points at that tenant's workload path.

Support additionally owns five independent release units. Declare these
Kustomizations with the same `flux-tenant` identity and source. Each must use
`wait: true` and `timeout: 5m` so the tenant's resident 180-second availability
contract, rather than an earlier Flux timeout, decides rollout success:

| Kustomization | Path |
|---|---|
| `support-case-intake` | `./labs/rbac-multitenant-isolation/tenants/support-release/case-intake` |
| `support-eligibility-cache` | `./labs/rbac-multitenant-isolation/tenants/support-release/eligibility-cache` |
| `support-evidence-index` | `./labs/rbac-multitenant-isolation/tenants/support-release/evidence-index` |
| `support-search` | `./labs/rbac-multitenant-isolation/tenants/support-release/support-search` |
| `support-response-template` | `./labs/rbac-multitenant-isolation/tenants/support-release/response-template` |

The Support quota must admit its seven-pod baseline and five simultaneous surge
pods while retaining one-pod headroom: `pods=13`, `requests.cpu=80m`,
`requests.memory=256Mi`, `limits.cpu=450m`, and `limits.memory=576Mi`.

Do not grant Secret access, RoleBinding mutation, Namespace mutation, CRD
mutation, or cluster-scoped authority. Workloads require Deployments,
ReplicaSets, Services, ConfigMaps, ServiceAccounts, Pods/log, NetworkPolicies,
and CiliumNetworkPolicies.

The exercise requirements and validation commands are in the lab README. The
files under `examples/` are structural prompts, not valid solutions.
