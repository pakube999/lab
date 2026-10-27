# ADR-008: Ansible for Node Bootstrap and OS Configuration

## Status
Accepted — planned, not yet implemented

## Context
The homelab IaC stack covers SaaS configuration (Terraform) and cluster
workloads (ArgoCD) but has a gap at the OS layer. Currently the k3s nodes
were configured manually — packages installed, SSH keys added, k3s installed
via the install script, NFS mounts configured. This process is undocumented
and not reproducible without manual effort.

With a planned migration to a new three-node HA k3s cluster, this gap becomes
significant. Manually configuring three nodes consistently is error-prone,
and the k3s HA setup (embedded etcd, token sharing between nodes) is
particularly sensitive to configuration inconsistencies.

## Decision
Ansible will be used to manage the OS layer — node bootstrap, package
installation, SSH configuration, NFS mounts, and k3s installation.

The Ansible playbooks will live in a separate repo (`pakube999/ansible`)
following the same pattern as the Terraform infrastructure repo — each
tool at the layer it is best suited for.

Planned structure:

ansible/
├── inventory/
│ └── hosts.yml — node IPs and groups
├── group_vars/
│ └── all.yml — common variables
├── roles/
│ ├── common/ — packages, SSH, timezone, NFS
│ ├── k3s_server/ — control plane install + etcd HA
│ └── k3s_agent/ — worker node install
└── site.yml — main playbook


## Why Ansible over alternatives

**Shell scripts** — not idempotent. Running twice produces unpredictable
results. No inventory management, no templating, no role reuse.

**Terraform** — not designed for OS configuration. The `remote-exec`
provisioner exists but is limited and not idempotent. Terraform is the
right tool for APIs, not SSH-based configuration management.

**cloud-init** — good for initial VM provisioning, less suited for
ongoing configuration management and day-2 operations.

**Ansible** — idempotent by design, SSH-based (no agent required),
YAML-based playbooks, mature role ecosystem, widely used in platform
engineering.

## Consequences

- Node bootstrap becomes reproducible — new node added to inventory,
  `ansible-playbook site.yml` configures it identically to existing nodes
- k3s HA cluster setup is documented as executable code not a runbook
- SSH key management, package versions, and NFS mounts are version
  controlled
- Not yet implemented — will be built during new cluster migration
- Adds a fourth repo to the IaC stack alongside lab, infrastructure,
  and the site repo

## The full IaC stack

| Layer | Tool | Repo | Status |
|-------|------|------|--------|
| SaaS config | Terraform | infrastructure (private) | ✅ Active |
| OS config | Ansible | ansible (planned) | 🔲 Planned |
| Cluster workloads | ArgoCD | lab (this repo) | ✅ Active |
| App packaging | Helm | upstream charts | ✅ Active |
| Secret management | SealedSecrets | lab (this repo) | ✅ Active |
| CI validation | GitHub Actions | lab (this repo) | ✅ Active |
