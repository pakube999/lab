# ADR-009: Network Policies — Deferred to New Cluster

## Status
Accepted — deferred

## Context
Kubernetes NetworkPolicy resources restrict pod-to-pod communication.
By default k3s allows all pods to communicate with all other pods across
all namespaces. Network policies would restrict this to explicit allow
rules, improving the security posture of the cluster.

## Investigation

The cluster runs k3s with embedded Flannel as the CNI:

/var/lib/rancher/k3s/agent/etc/cni/net.d/10-flannel.conflist


Standard Flannel does **not** enforce NetworkPolicy. Policies would be
accepted by the Kubernetes API but silently ignored — no actual
restriction would occur.

Enforcing NetworkPolicy requires either:
- **Canal** — Flannel + Calico policy enforcement, supported by k3s
- **Cilium** — eBPF-based CNI, more capable but more complex

## Decision

Network policies are deferred to the new three-node cluster.

Replacing the CNI on a running 2-node cluster carries significant risk:
- Brief network outage during transition
- Pods may need restarting
- IP assignments may change
- No HA — a failed CNI replacement could take the cluster down entirely

The security benefit on a LAN-only homelab is real but limited. The
risk of retrofitting onto a running cluster outweighs the benefit.

## New Cluster Approach

On the new three-node cluster, configure k3s at install time with
Cilium as the CNI:

```bash
curl -sfL https://get.k3s.io | sh -s - \
  --flannel-backend=none \
  --disable-network-policy \
  --cluster-init
```

Then install Cilium via Helm before deploying any workloads. NetworkPolicy
resources will be written for each namespace as part of the initial
cluster setup — enforced from day one rather than retrofitted.

## Consequences

- No network policy enforcement on current cluster — accepted risk
- New cluster will have proper CNI and NetworkPolicy from day one
- Policy manifests will live in the lab repo alongside app manifests
- Cilium also provides observability (Hubble UI) — additional benefit
  over Canal

## References

- k3s CNI options: https://docs.k3s.io/networking/basic-network-options
- Cilium k3s install: https://docs.cilium.io/en/stable/installation/k3s/
