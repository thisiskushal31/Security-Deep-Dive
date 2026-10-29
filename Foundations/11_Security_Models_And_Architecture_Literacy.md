# Security models and architecture literacy

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Before AppSec and System-Design patterns, know the classic **security models** and architecture ideas that policies and products still echo. Deep HLD patterns: [System-Design Security](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security). Wire architecture: Networks-Deep-Dive.

## Concept

## Why models exist

Formal models describe allowed information flows. Real systems approximate them with labels, roles, and enclaves. Knowing the names stops you from confusing “integrity model” with “confidentiality model.”

## Trust vs trustworthiness

| Term | Meaning |
|------|---------|
| Trust | A decision to rely on a party or component |
| Trustworthiness | Evidence-backed properties that justify (or refute) that reliance |

Security engineering aims to raise **trustworthiness** (measurable controls, assurance, transparency) so trust decisions are not blind. Zero Trust reduces standing trust in the network ([13](./13_Zero_Trust_And_Deception_Basics.md)).

## Confidentiality-oriented models (awareness)

| Model | Core idea |
|-------|-----------|
| Bell–LaPadula | No read up; no write down (protect confidentiality of classified labels) |
| Brewer–Nash (Chinese Wall) | Conflict-of-interest: access to one firm’s data can block another’s |

## Integrity-oriented models (awareness)

| Model | Core idea |
|-------|-----------|
| Biba | No read down; no write up (protect integrity of labels) |
| Clark–Wilson | Well-formed transactions and separation of duty for commercial integrity |

You rarely implement these models by name in a product company. You *do* implement related ideas: mandatory labels in high-assurance systems, segregation of duties, and constrained interfaces for sensitive transactions.

## Capability and access-matrix ideas

| Idea | Meaning |
|------|---------|
| Access control matrix | Subjects × objects with allowed operations |
| ACL | Per-object list of who can do what |
| Capability | Unforgeable token of rights a subject holds |

RBAC/ABAC ([7](./7_AAA_And_Access_Fundamentals.md)) are practical ways to manage the matrix at scale.

## Advanced

## Architecture building blocks

| Concept | Meaning |
|---------|---------|
| Trust boundary | Where trust level changes; must be authenticated and authorized ([12](./12_STRIDE_And_Threat_Modeling.md)) |
| Defense in depth | Layered controls ([2](./2_Controls_And_Physical.md)) |
| Isolation / sandboxing | Limit blast radius of code and workloads |
| Secure defaults | Products ship closed; opening access is deliberate |
| Fail secure | Prefer deny on failure when confidentiality/integrity dominate |
| Economy of mechanism | Prefer simple, reviewable designs |
| Complete mediation | Check authorization on every access, not once at login only |
| Least common mechanism | Minimize shared state that can leak across users |
| Deny by default | Absent an allow rule, access fails closed |
| Open design | Security does not rely on hiding the design; rely on keys and correct implementation |
| Separation of privilege | Require multiple conditions or parties for sensitive acts |

```text
External actors
   │ trust boundary
   ▼
Edge / PEP
   │ trust boundary
   ▼
Services (least privilege)
   │
   ▼
Data stores (classified, encrypted)
```

## Enterprise architecture vs product architecture

| View | Security question |
|------|-------------------|
| Enterprise | Where are identity, logging, and network segmentation shared? |
| Product / system | What are this service’s trust boundaries and data classes? |

Zero Trust moves the emphasis from a single hard perimeter to per-resource decisions ([13](./13_Zero_Trust_And_Deception_Basics.md)).

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] Contrast Bell–LaPadula vs Biba in one sentence each  
- [ ] Explain complete mediation vs “auth once at login”  
- [ ] Mark two trust boundaries on a simple three-tier sketch  

## Next

- [STRIDE and threat modeling](./12_STRIDE_And_Threat_Modeling.md)

## Further reading

- [NIST SP 800-160 Vol. 1 Rev. 1 — Engineering Trustworthy Secure Systems](https://csrc.nist.gov/pubs/sp/800/160/v1/r1/final)
- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final)
- [System-Design-Concepts Security](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security)
