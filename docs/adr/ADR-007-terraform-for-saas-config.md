# ADR-007: Terraform for SaaS Configuration, ArgoCD for Cluster Workloads

## Status
Accepted

## Context
The homelab stack has two distinct configuration surfaces:

1. **Cluster workloads** — what runs inside k3s (apps, ingress, monitoring)
2. **SaaS configuration** — GitHub repo settings, Cloudflare DNS, branch protection rules

Both need to be managed as code. The question is which tool manages which surface.

## Decision
- **ArgoCD** manages everything inside the cluster — Helm charts, manifests, secrets
- **Terraform** manages SaaS configuration — GitHub and Cloudflare resources

These live in separate repos:
- `pakube999/lab` — GitOps cluster repo (this repo)
- `pakube999/infrastructure` — Terraform SaaS configuration (private)

## Why not Terraform for Kubernetes?
Terraform has a Kubernetes provider and a Helm provider. Using them would create two competing sources of truth for cluster state — Terraform's state file and ArgoCD's live reconciliation. When they disagree, debugging becomes complex. ArgoCD was purpose-built for Kubernetes GitOps; Terraform was not.

## Why not ArgoCD for DNS/GitHub?
ArgoCD is a Kubernetes controller — it manages Kubernetes resources. GitHub branch protection and Cloudflare DNS records are not Kubernetes resources. Forcing them into ArgoCD via custom controllers adds unnecessary complexity.

## Consequences
- Clean separation of concerns — each tool at the layer it is best suited for
- SaaS configuration changes go through `terraform plan` → review → `terraform apply`
- Cluster changes go through PR → CI → ArgoCD sync
- State management is separate — Terraform local state (gitignored), ArgoCD live state in cluster
- Drift detection works independently for each surface

## The full IaC stack

| Layer | Tool | Repo |
|-------|------|------|
| SaaS config (GitHub, Cloudflare) | Terraform | infrastructure (private) |
| Cluster workloads | ArgoCD | lab (this repo) |
| App packaging | Helm | upstream charts |
| Secret management | SealedSecrets | lab (this repo) |
| CI validation | GitHub Actions | lab (this repo) |

