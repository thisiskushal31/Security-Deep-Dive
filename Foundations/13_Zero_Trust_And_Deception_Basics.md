# Zero Trust and deception basics

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Do not treat network location as proof of trust. Use decoys that create high-signal alerts when touched.

Product depth: [Identity/2](../Identity/2_Zero_Trust.md). Design trade-offs: [System-Design Security-Tradeoffs/2](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security-Tradeoffs/2_Auth_Design_vs_Zero_Trust.md). Deception inventory: [2](./2_Controls_And_Physical.md). Network shape literacy: [14](./14_Defensible_Network_Architecture_Literacy.md).

## Concept

### Zero Trust

Zero trust is a set of cybersecurity paradigms that move defenses from static, network-based perimeters to focus on users, assets, and resources. A zero trust architecture (ZTA) assumes no implicit trust based solely on physical or network location, or on asset ownership. Authentication and authorization (subject and device) are discrete functions performed before a session to a resource is established. The focus is protecting **resources**, not network segments alone.

It is a strategy, not a single product.

### Tenets (architecture literacy)

| Tenet | Meaning |
|-------|---------|
| All data sources and computing services are resources | Protect APIs, data, and workloads — not only “the LAN” |
| Secure all communication | Regardless of network location |
| Per-connection access | Grant access to a resource for that session — not a permanent blanket |
| Dynamic policy | Decisions use identity, requesting system state, and environment (and may include behavior) |
| Maintain system integrity | Keep enterprise systems in a secure state and monitor that they stay that way |
| Dynamic authentication | Enforce authentication before authorized access; re-evaluate as context changes |

```text
Assume breach / no implicit trust zone
  → Authenticate subject and device
  → Authorize this request to this resource
  → Least privilege + segment blast radius
  → Monitor and adapt policy
```

## Advanced

### Control plane and data plane

Zero Trust deployments separate **policy decision/admin** traffic from **application** traffic:

| Plane | Role |
|-------|------|
| **Control plane** | Components that decide and manage access policy |
| **Data plane** | Path the application traffic takes once (or while) policy allows it |

Core logical components:

| Component | Role |
|-----------|------|
| **Policy engine (PE)** | Ultimate decision: allow / deny (and related decisions) based on policy and inputs |
| **Policy administrator (PA)** | Executes the decision — establishes or tears down the session path via the enforcement point |
| **Policy enforcement point (PEP)** | Gateway, agent, or proxy that enables, monitors, and terminates connections between subject and resource |

Supporting inputs commonly include identity management, enterprise PKI, device/posture (CDM-class) data, threat intelligence, activity logs / SIEM, and compliance constraints.

| Idea | Meaning |
|------|---------|
| Adaptive identity | Identity and risk signals influence ongoing access |
| Threat scope reduction | Limit what a compromised identity or device can reach |
| Policy-driven access control | Allow/deny follows explicit policy evaluated by the policy engine |
| Implicit trust zone | “Inside the network = trusted” — remove as a sole control |
| Subject / system | The user or workload and the device/workload context making the request |

### Maturity pillars (operational map)

```text
Identity | Devices | Networks | Applications and workloads | Data
        \______ visibility & analytics · automation · governance ______/
```

Use pillars as a coverage checklist across the program — not a mandate to buy one box per pillar.

## Apply

### Builder checklist

| Do | Don't |
|----|-------|
| Strong identity for people and workloads | Flat shared credentials |
| Short-lived credentials and a working revoke path | “Inside the VPN” as the only control |
| Segment by sensitivity and identity | One flat security group for every workload |
| Authorize at the system that owns the data | Assume edge TLS is authorization |
| Log denials and anomalies | Skip MFA hygiene after buying a gateway |

### Deception technology

| Technology | Signal |
|------------|--------|
| Honeypot / honeynet | Interaction with a decoy system or network |
| Honeyfile | Unexpected open or read of a decoy file |
| Honeytoken | Use of a decoy credential or token |

Rules: only on systems you own; clear labels for responders; alert routing into [Defensive-Ops](../Defensive-Ops/README.md); never use decoys instead of patching and least privilege.

### Where depth continues

| Topic | Home |
|-------|------|
| Federation and Zero Trust program notes | [Identity](../Identity/README.md) |
| Tokens, mTLS, gatekeeper trade-offs | System-Design Security-Tradeoffs |
| Service mesh / wire | Networks-Deep-Dive |
| Alert handling | Defensive-Ops |

## Self-check

- [ ] Define Zero Trust without naming a vendor product  
- [ ] Name PE, PA, and PEP roles  
- [ ] List five maturity pillars  
- [ ] Give one honey* example and the response when it fires  

## Next

- [Defensible network architecture literacy](./14_Defensible_Network_Architecture_Literacy.md)

## Further reading

- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
- [CISA Zero Trust Maturity Model](https://www.cisa.gov/zero-trust-maturity-model)
