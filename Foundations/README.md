# Foundations

[← README](../README.md)

General security concepts before AppSec, cloud, SOC, CTI practice, threat-modeling workshops, or lab offense.

**How this path is ordered:** each note and the sequence itself move **concept → advanced → apply**. *Apply* at Foundations means literacy and builder habits — not specialty implementation (that lives in AppSec, Offensive, Defensive-Ops, Networks, Tooling, and the practice folders below).

Practice deep dives beyond literacy: [Vulnerability-Management](../Vulnerability-Management/README.md) · [Threat-Modeling](../Threat-Modeling/README.md) · [Threat-Intelligence](../Threat-Intelligence/README.md).

Threat actors and vectors continue in [Threats](../Threats/README.md). Framework nesting: [GRC](../GRC/README.md). Authorized offense: [Offensive](../Offensive/README.md). Detection/IR: [Defensive-Ops](../Defensive-Ops/README.md).

## Learning path (basic → advanced → apply)

```text
Part A  Bedrock          1 → 3
Part B  Program frame    4 → 6
Part C  Core technical   7 → 9
Part D  Design           10 → 13
Part E  Build & operate  14 → 17
Part F  Adversary & ops  18 → 21  → Threats
```

### Part A — Bedrock concepts

| # | Topic |
|---|--------|
| 1 | [CIA and risk](./1_CIA_And_Risk.md) |
| 2 | [Controls, physical, deception](./2_Controls_And_Physical.md) |
| 3 | [Ethics, authorization, and lab rules](./3_Ethics_Authorization_And_Lab_Rules.md) |

### Part B — Program frame

| # | Topic |
|---|--------|
| 4 | [Security policy, governance, and roles](./4_Security_Policy_Governance_And_Roles.md) |
| 5 | [Risk management process and RMF](./5_Risk_Management_Process_And_RMF.md) |
| 6 | [Assets, data states, and assurance](./6_Assets_Data_States_And_Assurance.md) |

### Part C — Core technical literacy

| # | Topic |
|---|--------|
| 7 | [AAA and access fundamentals](./7_AAA_And_Access_Fundamentals.md) |
| 8 | [Cryptographic solutions literacy](./8_Cryptographic_Solutions_Literacy.md) |
| 9 | [Change management and security](./9_Change_Management_And_Security.md) |

### Part D — Design and architecture

| # | Topic |
|---|--------|
| 10 | [Secure design principles](./10_Secure_Design_Principles.md) |
| 11 | [Security models and architecture literacy](./11_Security_Models_And_Architecture_Literacy.md) |
| 12 | [STRIDE and threat modeling](./12_STRIDE_And_Threat_Modeling.md) |
| 13 | [Zero Trust and deception basics](./13_Zero_Trust_And_Deception_Basics.md) |

### Part E — Build and operate (foundation apply)

| # | Topic |
|---|--------|
| 14 | [Defensible network architecture literacy](./14_Defensible_Network_Architecture_Literacy.md) |
| 15 | [Endpoint and system hardening literacy](./15_Endpoint_And_System_Hardening_Literacy.md) |
| 16 | [Human security literacy](./16_Human_Security_Literacy.md) |
| 17 | [Privacy literacy](./17_Privacy_Literacy.md) |

### Part F — Adversary view and operational literacy

| # | Topic |
|---|--------|
| 18 | [Adversary models, Kill Chain, and ATT&CK](./18_Adversary_Models_Kill_Chain_And_ATTCK.md) |
| 19 | [Threat intelligence literacy](./19_Threat_Intelligence_Literacy.md) |
| 20 | [Vulnerability management literacy](./20_Vulnerability_Management_Literacy.md) |
| 21 | [Resilience, continuity, and recovery literacy](./21_Resilience_Continuity_And_Recovery_Literacy.md) |

## Linear path

`1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → 11 → 12 → 13 → 14 → 15 → 16 → 17 → 18 → 19 → 20 → 21` → [Threats](../Threats/README.md)

## Course bridges after Foundations

| Goal | Next home |
|------|-----------|
| Threat modeling workshops | Note 12 · [Threat-Modeling](../Threat-Modeling/README.md) · [Security-Tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security-Tradeoffs) |
| CTI practice | Notes 18–19 · [Threat-Intelligence](../Threat-Intelligence/README.md) · [Defensive-Ops](../Defensive-Ops/README.md) |
| Penetration testing (lab) | Note 3 · [Offensive](../Offensive/README.md) · [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security) |
| Vuln program | Note 20 · [Vulnerability-Management](../Vulnerability-Management/README.md) |
| Wire / TLS depth | Note 14 · [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive) |

## Topic map

| Topic area | Home note |
|------------|-----------|
| CIA, McCumber, gap analysis, adversarial thinking | 1 |
| Control categories/types + NIST CSF functions | 2 |
| Authorization, RoE, lab ethics, PTES shape | 3 |
| Policy, roles, law/policy literacy | 4 |
| RMF / BIA / SCRM | 5 |
| Assets, data states, PII handling | 6 |
| AAA, SSO, credential hygiene, access matrix | 7 |
| Cryptographic solutions literacy | 8 |
| Change management | 9 |
| Secure design principles | 10 |
| Security models + trustworthiness | 11 |
| Threat modeling literacy | 12 |
| Zero Trust + deception | 13 |
| Defensible network architecture | 14 |
| Endpoint / system hardening | 15 |
| Human security / social engineering literacy | 16 |
| Privacy literacy | 17 |
| Kill Chain, Diamond, ATT&CK, Pyramid of Pain | 18 |
| CTI lifecycle, levels, STIX/TAXII, TLP | 19 |
| Vulnerability management literacy | 20 |
| Resilience / BC / DR / RTO-RPO | 21 |

Actors, vectors, vulns, mitigations → [Threats](../Threats/). Legal catalogs → [GRC](../GRC/). Hiring loops → out of scope.

Design-time examples: [System-Design Security-Tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security-Tradeoffs).
