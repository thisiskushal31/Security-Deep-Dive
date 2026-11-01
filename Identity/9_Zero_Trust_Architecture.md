# Zero Trust architecture

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

**Zero Trust (ZT)** is a set of cybersecurity paradigms that move defenses from static network perimeters to users, assets, and resources. A **Zero Trust Architecture (ZTA)** uses those principles to plan infrastructure and workflows. NIST SP 800-207 is the vocabulary most programs share. ZT is a **strategy**, not a single product SKU.

## Concept

### Operative idea

Do not grant implicit trust based on network location (inside LAN vs internet) or asset ownership alone. Authenticate and authorize **each session** to each resource from a posture that assumes the network is hostile.

| Tenet (SP 800-207 grain) | Practice |
|--------------------------|---------|
| All data sources and computing services are resources | Inventory beyond “the castle” |
| Secure all communication | Encrypt; authenticate endpoints |
| Access on a per-session basis | No standing broad trust after one VPN join |
| Access with least privilege, dynamic policy | Identity + device + context → decision |
| Measure integrity / posture continuously | Monitor; revoke |
| Strict authn/authz before access | Strong identity — this folder |
| Collect info to improve security posture | Telemetry → [Defensive-Ops](../Defensive-Ops/README.md) |

### Logical components

```text
Subject / workload ──▶ Policy Enforcement Point (PEP)
                              │
                              ▼
                      Policy Engine / Administrator
                              │
                    (identity, device, threat, … inputs)
```

| Piece | Role |
|-------|------|
| **Policy engine** | Decide allow / deny / step-up |
| **Policy administrator** | Enact decisions (tokens, gateway config) |
| **PEP** | Gate the resource (proxy, agent, API gateway, OS control) |

Buying ZTNA (Zero Trust Network Access) without identity hygiene, device inventory, and logging is perimeter theater with a new logo.

## Advanced

### Deployment models (conceptual)

| Model | Idea |
|-------|------|
| Device agent / gateway | Soft perimeter on each device or site |
| Enclave-based | Protect specific resource islands |
| Cloud / service-based | Policy points in front of SaaS and cloud APIs |
| Hybrid | Reality for most enterprises |

Map models to *your* resource graph — cloud-native access patterns deepen in [11](./11_Workload_And_Non_Human_Identity.md) (SP 800-207A).

### Identity is necessary, not sufficient

| Input to policy | Failure if missing |
|-----------------|-------------------|
| User / service identity | Shared accounts, flat VPN trust |
| Device identity & health | Malware on “trusted” laptop |
| Request context | Impossible risk-based auth |
| Resource classification | Same policy for public wiki and payroll |

Federation and authenticators: [1](./1_Digital_Identity_And_Assurance_Levels.md)–[8](./8_Tokens_Sessions_And_Client_Patterns.md). Maturity roadmap: [10](./10_Zero_Trust_Maturity_And_Identity_Pillar.md).

### Anti-patterns

| Anti-pattern | Better |
|--------------|--------|
| “VPN means trusted” | VPN + still verify app identity and device posture |
| Flat service mesh with one identity | Per-workload identity and policy |
| Forever sessions after SSO | Re-auth on risk; working revoke path |
| ZT project owned only by networking | Co-own with identity, endpoint, app owners |

HLD trade-off depth: [System-Design Auth vs Zero Trust](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security-Tradeoffs/2_Auth_Design_vs_Zero_Trust.md).

## Apply

1. List five critical resources that today trust “corp network” alone.  
2. Name the PEP in front of each (or admit there is none).  
3. For one resource, write the policy inputs you *actually* have today (identity only? device?).  
4. Pick one standing broad access path to kill (shared jump box, any-any SG).  
5. Align logging so denials and step-ups are visible to detection — [Defensive-Ops](../Defensive-Ops/README.md).

## Next

- [Zero Trust maturity and the identity pillar](./10_Zero_Trust_Maturity_And_Identity_Pillar.md)

## Further reading

- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
- [NIST SP 800-207A — ZTA for cloud-native / multi-location access](https://csrc.nist.gov/pubs/sp/800/207/a/final)
