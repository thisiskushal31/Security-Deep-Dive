# Supply chain (cloud / delivery)

[← Cloud-Security](./README.md)

You inherit risk from **dependencies, base images, build systems, and vendors**. Program controls: know what you ship, prove who built it, refuse tampered artifacts.

## Controls (map)

| Control | Job |
|---------|-----|
| **Lockfiles / pins** | Reproducible dependency sets |
| **SCA** | Known vulns in packages |
| **SBOM** | Inventory of what is in the artifact |
| **Sign / attest** | Prove provenance (Cosign-class) |
| **Trusted builders** | Harden CI; OIDC to registries |
| **Admission verify** | Cluster/runtime rejects unsigned |

Ordered gates: [Handbook gate chain](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/4_Security_Gate_Chain.md) and [CiCd/6 Supply chain](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/CiCd/6_Supply_Chain_And_Signing.md). Tool literacy: Cosign, Trivy, Snyk folders under Handbook Security.

## Program habits

1. Prefer minimal base images; rebuild on a cadence.  
2. Separate build identities from deploy identities.  
3. Treat a compromised CI secret as production incident fuel.  
4. Track critical vendor advisories for anything in the trust path.

## Related

- Threats supply-chain vector → [Threats/2](../Threats/2_Vectors_And_Surfaces.md)
- Do not rewrite Cosign tutorials here — link Handbook/Tooling.
