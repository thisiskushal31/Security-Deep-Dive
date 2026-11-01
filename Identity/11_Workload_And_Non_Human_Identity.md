# Workload and non-human identity

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

Users are not the only principals. **Services, jobs, devices, and agents** call APIs across clouds and clusters. NIST SP 800-207A focuses Zero Trust access control on **cloud-native, multi-location** applications — identity shifts from network location to **application and service identities**, enforced via gateways, sidecars, and frameworks such as **SPIFFE (Secure Production Identity Framework for Everyone)**.

## Concept

### Why NHI (non-human identity) breaks classical SSO stories

| Human SSO assumption | Workload reality |
|----------------------|------------------|
| Interactive browser login | No human in the loop |
| Long-lived password/API key in config | Keys leak via repos, images, logs |
| One VPN for “prod” | Multi-cloud; ephemeral tasks; autoscaling |

```text
Workload A ──mTLS / JWT──▶ PEP / sidecar ──policy──▶ Workload B
                 ▲
                 │
         Platform issues short-lived identity (SPIFFE ID, cloud SA, …)
```

### Identity vs secret

| Approach | Grain |
|----------|-------|
| **Shared static secret** | Easy; catastrophic blast radius |
| **Platform-issued short-lived credential** | Instance identity; rotate by design |
| **SPIFFE ID + SVID (SPIFFE Verifiable Identity Document)** | Portable workload name + cryptographic proof |
| **Cloud IAM roles / federated OIDC** | Cloud-native; still need least privilege |

CI OIDC to cloud roles: [DevOps-Handbook](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/5_OIDC_CI_And_Least_Privilege.md). Agent/LLM non-human identity threats: [AI-Security](../AI-Security/README.md).

## Advanced

### SP 800-207A building blocks

| Building block | Role |
|----------------|------|
| **API gateway / ingress** | North-south PEP (policy enforcement point) |
| **Sidecar / mesh proxy** | East-west authn/z without app rewrites |
| **Service identity infrastructure** | Issue/validate workload identities (e.g., SPIFFE/SPIRE) |
| **Policy** | Identity + method + resource — not only CIDR |

Multi-cloud means the policy system must not assume a single network fabric — identity becomes the stable handle.

### SPIFFE literacy (program level)

| Term | Meaning |
|------|---------|
| **SPIFFE ID** | URI naming a workload (`spiffe://trust-domain/path`) |
| **SVID** | X.509 or JWT document proving that ID |
| **Trust domain** | Administrative boundary for issuance |
| **Workload API** | How agents deliver SVIDs to workloads |

Install/ops detail belongs with your mesh/platform Tooling — here the requirement is: **authenticate services as first-class principals**.

### Anti-patterns

| Anti-pattern | Better |
|--------------|--------|
| One cluster-wide service account | Per-workload identity + narrow authZ |
| API keys in environment variables committed forever | Short-lived SVIDs / cloud temp creds |
| mTLS without identity (any cert) | SPIFFE-style identity in the cert SAN (Subject Alternative Name) |
| Humans sharing break-glass keys in chat | JIT (just-in-time) human access + audited NHI paths |

Cloud control-plane IAM: [Cloud-Security](../Cloud-Security/README.md). Wire/mesh: Networks / Containerization deep dives.

## Apply

1. Inventory top 10 non-human callers of sensitive APIs (jobs, meshes, functions, partners).  
2. Mark each: static secret vs platform identity vs unknown.  
3. Pick one static key to replace with short-lived workload identity.  
4. Ensure east-west traffic for that service pair requires verified identity, not only network allow-lists.  
5. Add NHI join/leave (deploy/destroy) to the same offboarding mindset as humans.

Tool installs for SPIRE / OpenBao / Teleport: [16](./16_Identity_Tooling_Map.md).

## Next

- [Identity threats, detect, and harden](./12_Identity_Threats_Detect_And_Harden.md)

## Further reading

- [NIST SP 800-207A](https://csrc.nist.gov/pubs/sp/800/207/a/final)
- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
- [SPIFFE — Overview](https://spiffe.io/docs/latest/spiffe-about/overview/)
