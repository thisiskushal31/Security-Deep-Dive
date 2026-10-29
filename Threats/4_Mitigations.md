# Mitigations

[← Threats](./README.md)

Mitigations reduce likelihood or impact. Prefer **prevent** where cheap; always pair with **detect** and **recover**.

## Core mitigation families

| Family | What you do | Where depth lives |
|--------|-------------|-------------------|
| **Patch / update** | Fix known vulns on a written SLA | Handbook vuln mgmt; SCA gates |
| **Harden** | Safe defaults, disable unused features, CIS-class baselines | Cloud-Security; OS notes in Handbook |
| **Segment** | Limit blast radius (network, account, namespace, identity) | Networks; Zero Trust ([Identity](../Identity/README.md)) |
| **Authenticate / authorize** | Strong identity; least privilege | Identity; System-Design Security patterns |
| **Encrypt** | In transit and at rest with managed keys | Cryptography pitfalls; Networks TLS |
| **Validate input / safe design** | Stop injection and abuse classes | [AppSec](../AppSec/README.md) |
| **Detect** | Logs, SIEM, EDR, CSPM alerts | [Defensive-Ops](../Defensive-Ops/README.md) |
| **Respond / recover** | IR playbooks, backups that restore | Defensive-Ops; DR patterns in System-Design |

## Mapping back to risk

For each high risk from threat modeling:

1. Pick at least one **preventive** control.  
2. Add a **detective** control if residual risk matters.  
3. Confirm a **corrective** path (revoke, restore, rotate).  
4. Name an **owner** and a review date ([Foundations/3](../Foundations/3_Controls_And_Physical.md)).

## Do not rewrite the gate chain here

Ordered PR→prod controls: [DevOps-Handbook Security gate chain](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/4_Security_Gate_Chain.md). This folder stops at **program** mitigation types.

## Next

- [AppSec](../AppSec/README.md) · [Defensive-Ops](../Defensive-Ops/README.md) · [GRC](../GRC/README.md)
