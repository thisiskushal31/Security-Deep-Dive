# STRIDE and threat modeling

[← Foundations](./README.md)

Threat modeling is a **structured way to ask what can go wrong** before you ship a new trust boundary. STRIDE is one common checklist (Microsoft origin; still widely taught).

## STRIDE

| Letter | Threat class | Question on a diagram |
|--------|--------------|------------------------|
| **S**poofing | Pretend to be someone/something else | Can a caller forge identity? |
| **T**ampering | Change data or code in transit or at rest | What is integrity-protected? |
| **R**epudiation | Deny an action afterward | Do we have trustworthy logs? |
| **I**nformation disclosure | See data you should not | Where does sensitive data flow? |
| **D**enial of service | Make the system unusable | What is the cheapest way to exhaust us? |
| **E**levation of privilege | Gain rights you should not have | Where do trust boundaries allow a jump? |

Walk **data flows** and **trust boundaries** (user → edge → service → data store), not a random brainstorm.

## When a light model is enough vs a full review

| Situation | Enough |
|-----------|--------|
| Small internal tool, no new data class | Short STRIDE on the PR / design note |
| New public API, PII, payments, or third-party trust | Facilitated session + written mitigations |
| Major architecture change | Model + [Security-Tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security-Tradeoffs) + AppSec gates |

**Output:** prioritized mitigations with owners — not a pretty diagram alone. Link mitigations into [Threats/4](../Threats/4_Mitigations.md) and delivery gates in [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).

## Design-time home

HLD annotations, URL-shortener-style worked examples, and performance/crypto trade-offs live in System-Design:

- [Threat modeling at design time](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security-Tradeoffs/1_Threat_Modeling_At_Design_Time.md)

This file is the **program language** (what STRIDE means and when to run it).

## Related home (do not rewrite here)

- Wire / TLS / firewalls → Networks-Deep-Dive `Security/`
- Pipeline gates → DevOps-Handbook `Security/`
- OAuth stack how-to → Tooling `Specs-Standards/OAuth/`

## Next

- [Controls and physical](./3_Controls_And_Physical.md)
- [Threats](../Threats/README.md)
