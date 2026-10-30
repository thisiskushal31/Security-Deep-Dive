# Threat modeling

[← README](../README.md)

Workshop and method **depth** beyond foundation literacy — enough in these notes to run sessions, walk STRIDE-per-element, stage PASTA, build attack trees, apply LINDDUN, close tickets/tests, and threat-model **LLM/agentic** systems. Further reading is optional enrichment, not required to learn the craft.

Literacy intro: [Foundations/12](../Foundations/12_STRIDE_And_Threat_Modeling.md). Adversary models: [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md). Secure SDLC: [AppSec](../AppSec/README.md). Design examples: [System-Design Security-Tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security-Tradeoffs).

Each note follows **concept → advanced → apply**. Inside a note: explain in text, then an official image when one fits, then a short diagram only if it still helps.

```text
1 Workshop process → 2 DFD + STRIDE practice → 3 PASTA
  → 4 Attack trees + LINDDUN → 5 Tooling + close the loop
  → 6 AI / agentic threat modeling
```

## Topic files

| # | Topic |
|---|--------|
| 1 | [Workshop process and facilitation](./1_Workshop_Process.md) |
| 2 | [DFD and STRIDE in practice](./2_DFD_And_STRIDE_In_Practice.md) |
| 3 | [PASTA staged walkthrough](./3_PASTA_Staged_Walkthrough.md) |
| 4 | [Attack trees and LINDDUN](./4_Attack_Trees_And_LINDDUN.md) |
| 5 | [Tooling and closing the loop](./5_Tooling_And_Closing_The_Loop.md) |
| 6 | [AI and agentic threat modeling](./6_AI_And_Agentic_Threat_Modeling.md) |

## Learning path

`1 → 2 → 3 → 4 → 5 → 6`

Skip to **6** after **2** if you already run classical workshops and only need the AI overlay. Do **not** skip DFD/STRIDE entirely — note 6 assumes that craft.

## Topic map

| Area | Home |
|------|------|
| Four questions, attacker model, EoP/Cornucopia, mature-team traps | 1 |
| DFD hygiene (data labels, CI/CD, zoom level), STRIDE-per-element | 2 |
| PASTA seven stages with DoD, escalate-from-STRIDE | 3 |
| Attack trees (AND/OR); full LINDDUN type depth | 4 |
| Dragon/TMT/pytm/Threagile; ASVS; detection+prevention; Q4 | 5 |
| LLM/ASI checklists, MAESTRO layers, ATLAS nesting, AI DFD extensions | 6 |

## Related

- Privacy literacy → [Foundations/17](../Foundations/17_Privacy_Literacy.md)
- Change / revisit → [Foundations/9](../Foundations/9_Change_Management_And_Security.md)
- Accept residual risk → [Vulnerability-Management/4](../Vulnerability-Management/4_Exception_And_Acceptance_Workflows.md)
- AI-era threat literacy → [Threats/8](../Threats/8_AI_Era_Threats.md)
- AI build/operate depth → [AI-Security](../AI-Security/README.md)
