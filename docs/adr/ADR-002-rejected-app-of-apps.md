# ADR-002: Rejected App-of-Apps Pattern for ArgoCD

## Status
Accepted

## Context
ArgoCD supports an "app of apps" pattern where a parent Application
manages child Application manifests, allowing ArgoCD to manage its own
config recursively — including infra components like ingress and
cert-manager.

## Decision
Not adopting app-of-apps at this scale. ArgoCD Application manifests
are applied manually via kubectl apply.

## Consequences
- Simpler mental model — no recursive dependency chain to reason about
- Easier debugging — during the homepage outage, not having app-of-apps
  meant one less layer of indirection to rule out when root-causing
- Manual kubectl apply required for each new app — acceptable overhead
  for a single-operator cluster
- Would reconsider at team scale where coordination overhead justifies it

## Known Gap
ArgoCD Application manifests in `infra/argocd/applications/` require manual
`kubectl apply` after any change — ArgoCD does not watch its own Application
manifests without app-of-apps or an ApplicationSet. This is a documented
tradeoff at this scale. A future ApplicationSet could close this gap without
the full recursion risk of app-of-apps.
