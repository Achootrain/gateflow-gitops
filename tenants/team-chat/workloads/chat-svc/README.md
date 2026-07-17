# chat-svc deployment wiring

Before this deployment can authenticate to Key Vault, replace both
`<CHAT_SVC_CLIENT_ID>` values with the platform stack output:

```bash
pulumi stack output chatSvcClientId
```

Also replace `<AZURE_TENANT_ID>` with the Microsoft Entra tenant ID
(`az account show --query tenantId -o tsv`). These are identifiers, not
secrets; the Gemini API key is fetched only by the CSI driver and is never
committed to Git.

The workload identity federated credential is pinned to
`system:serviceaccount:team-chat:chat-svc` — renaming this namespace or
ServiceAccount requires a matching platform-layer change.
