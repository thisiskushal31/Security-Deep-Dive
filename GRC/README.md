# GRC

[← README](../README.md)

Governance, risk, compliance — **framework nesting and assurance literacy**, not legal advice. Use this to speak the same language as auditors, framework docs, and security program owners.

Risk *process* intro stays in [Foundations/5](../Foundations/5_Risk_Management_Process_And_RMF.md). Policy/roles intro: [Foundations/4](../Foundations/4_Security_Policy_Governance_And_Roles.md). Adversary TTPs: [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md) · AI threats: [Threats/8](../Threats/8_AI_Era_Threats.md).

Each note follows **concept → advanced → apply** (basics → depth → implementation habits). Topic flow inside a note: explain in text, then an official image when one fits, then a short diagram only if it still helps.

```text
1 Nesting → 2 CSF 2.0 → 3 RMF → 4 Control catalogs
  → 5 ISO 27001 ISMS → 6 SOC 2 / evidence → 7 NICE workforce
```

## Topic files

| # | Topic | Status |
|---|--------|--------|
| 1 | [Framework nesting (NIST, ISO, ATT&CK)](./1_NIST_ISO_And_ATTCK.md) | filled |
| 2 | [NIST CSF 2.0 outcomes](./2_NIST_CSF_2_Outcomes.md) | filled |
| 3 | [NIST RMF lifecycle](./3_NIST_RMF_Lifecycle.md) | filled |
| 4 | [Control catalogs (800-53 and CIS)](./4_Control_Catalogs_800-53_And_CIS.md) | filled |
| 5 | [ISO 27001 ISMS literacy](./5_ISO_27001_ISMS_Literacy.md) | filled |
| 6 | [SOC 2 and evidence](./6_SOC2_And_Evidence.md) | filled |
| 7 | [NICE workforce literacy](./7_NICE_Workforce_Literacy.md) | filled |

## Learning path

`1 → 2 → 3 → 4 → 5 → 6 → 7`

## Topic map

| Area | Home |
|------|------|
| Nesting layers; ATT&CK vs compliance; AI RMF pointer | 1 |
| CSF 2.0 Functions, Profiles, Tiers | 2 |
| RMF Prepare→Monitor (system authorization path) | 3 |
| SP 800-53 families; CIS Controls role | 4 |
| ISMS / ISO 27001 literacy | 5 |
| SOC 2 Type I/II; engineer evidence | 6 |
| NICE work-role literacy | 7 |

```text
Law / contract → ISMS → control catalogs → CSF outcomes
                      ↘ SOC 2 evidence
ATT&CK / ATLAS informs detections (not a compliance checklist)
NICE describes who does the work
```

## Adjacent (pointers, not separate GRC notes)

| Topic | Where |
|-------|--------|
| Privacy outcomes framework | [NIST Privacy Framework](https://www.nist.gov/privacy-framework) · [Foundations/17](../Foundations/17_Privacy_Literacy.md) |
| AI governance language | [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) · [Threats/8](../Threats/8_AI_Era_Threats.md) |
| FedRAMP (cloud ATO cousin) | [fedramp.gov](https://www.fedramp.gov/) · baselines in [4](./4_Control_Catalogs_800-53_And_CIS.md) |
| CISA CPGs 2.0 / sector SSGs | [CISA CPGs](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals) · [2](./2_NIST_CSF_2_Outcomes.md) |
| Official crosswalks | [CSF informative references](https://www.nist.gov/cyberframework/informative-references) · [CPRT](https://csrc.nist.gov/Projects/cprt) |

Out of scope for this folder: full 800-53 encyclopedia, paid ISO/CIS redistribution, auditor playbooks, CMMC/FedRAMP program ops deep dives.

## Related

- Live posture / patch SLA (delivery) → [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security)
- Threats and mitigations → [Threats](../Threats/README.md)
