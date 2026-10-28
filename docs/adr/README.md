# Architecture Decision Records

This folder documents significant architectural and design decisions made
during the evolution of this homelab cluster.

Each ADR captures the context, decision, and consequences at the time it
was made. They are not updated retroactively — if a decision changes, a
new ADR is written superseding the old one.

| ADR | Title | Status |
|-----|-------|--------|
| [ADR-001](ADR-001-k3s-over-full-kubernetes.md) | K3s over full Kubernetes | Accepted |
| [ADR-002](ADR-002-rejected-app-of-apps.md) | Rejected app-of-apps pattern | Accepted |
| [ADR-003](ADR-003-manual-sync-policy.md) | Manual sync policy initially | Accepted |
| [ADR-004](ADR-004-sealedsecrets-over-eso.md) | SealedSecrets over External Secrets Operator | Accepted |
| [ADR-005](ADR-005-remote-charts-over-local.md) | Remote Helm charts over local | Accepted |
| [ADR-006](ADR-006-deferred-image-scanning.md) | Deferred image scanning — Trivy supply chain incident | Accepted |
| [ADR-007](ADR-007-terraform-for-saas-config.md) | Terraform for SaaS config, ArgoCD for cluster workloads | Accepted |
| [ADR-008](ADR-008-ansible-for-node-bootstrap.md) | Ansible for node bootstrap and OS configuration | Accepted — planned |
| [ADR-009](ADR-009-network-policies-deferred.md) | Network policies deferred — Flannel CNI limitation | Accepted — deferred |
