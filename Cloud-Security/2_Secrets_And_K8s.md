# Secrets and Kubernetes security

[← Cloud-Security](./README.md)

How secrets and cluster identity fail in cloud-native delivery — program view. Helm/kubectl install stays in Tooling/Containers; cluster depth in Containerization-Deep-Dive.

## Secrets

| Anti-pattern | Better |
|--------------|--------|
| Secret in Git / plaintext ConfigMap | External store + CSI / ESO; sealed only if process is solid |
| Secret baked into image | Inject at runtime ([Handbook Vault](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security/Vault)) |
| One token for all namespaces | Per-workload policy paths |
| Forever static cloud keys on nodes | Workload identity / IRSA-class federation |

Practices overview: [Handbook Security/1](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/1_Security_Practices_And_Secrets.md).

## Kubernetes posture (KSPM grain)

| Area | Ask |
|------|-----|
| RBAC | Who can create pods that mount secrets or hostPath? |
| Privileged / capabilities | Are privileged pods justified and gated? |
| Images | Pinned digests? Scanned? From trusted registry? |
| NetworkPolicy | Default deny where needed? |
| etcd / control plane | Encrypted at rest; access limited |
| Admission | Kyverno/OPA policies on apply |

Container runtime and orchestration depth: [Containerization-Deep-Dive](https://github.com/thisiskushal31/Containerization-Deep-Dive). Policy-as-code tools: Handbook OPA/Kyverno.

## Next

- [Supply chain](./3_Supply_Chain.md)
