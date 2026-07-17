# production (placeholder)

No prod cluster exists yet. When it does: a prod `fluxConfiguration` (platform
layer, stack `prod`) reconciles `clusters/production`, which composes the same
`infrastructure/` and `tenants/` trees — with per-cluster patches here if prod
needs different values (replicas, retention, LB config).
