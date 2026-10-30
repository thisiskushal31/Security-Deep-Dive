# AI security (practice deep dive)

[← README](../README.md)

Build/operate depth for securing LLM apps and agents: threat-model operations, prompt-injection architecture, RAG/memory hardening, agentic controls, AIBOM/MCP supply chain, authorized evaluation, shadow-AI governance, and **attack mechanisms for defenders** (how injection/jailbreak/agency abuse work so you can stop them).

Literacy: [Threats/8](../Threats/8_AI_Era_Threats.md). Workshop method: [Threat-Modeling/6](../Threat-Modeling/6_AI_And_Agentic_Threat_Modeling.md).

Each note follows **concept → advanced → apply**. Official Further reading only. Lab/adversarial work stays **authorized and isolated** — [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md). Note [8](./8_Attack_Mechanisms_For_Defenders.md) teaches mechanisms and public OWASP/ATLAS patterns for defense and scoped testing — not a misuse cookbook against systems you do not own.

```text
1 Threat model in practice → 2 Prompt-injection architecture → 3 RAG/memory
  → 4 Agentic controls → 5 Supply chain / AIBOM / MCP
    → 6 Authorized evaluation → 7 Shadow AI / private GenAI
      → 8 Attack mechanisms for defenders
```

## Topic files

| # | Topic |
|---|--------|
| 1 | [LLM application threat model in practice](./1_LLM_App_Threat_Model_In_Practice.md) |
| 2 | [Prompt injection defenses (architecture)](./2_Prompt_Injection_Defenses.md) |
| 3 | [RAG, vector store, and memory hardening](./3_RAG_And_Memory_Hardening.md) |
| 4 | [Agentic controls](./4_Agentic_Controls.md) |
| 5 | [Model and data supply chain](./5_Model_And_Data_Supply_Chain.md) |
| 6 | [Authorized AI evaluation](./6_Authorized_AI_Evaluation.md) |
| 7 | [Shadow AI and private GenAI](./7_Shadow_AI_And_Private_GenAI.md) |
| 8 | [Attack mechanisms for defenders](./8_Attack_Mechanisms_For_Defenders.md) |

## Learning path

`1 → 2 → 3 → 4 → 5 → 6 → 7 → 8` (read **8** early if you need attack-mechanism context while building defenses)

## Topic map

| Area | Home |
|------|------|
| Component vs agent, lethal trifecta, LLM/ASI ownership, RMF Map, living register, release gates | 1 |
| Assume injection; plant→act chain; containment stack; schema/output; Unicode/modality | 2 |
| RAG poison path; ACL-before-retrieve; provenance; delimiters; embeddings; memory | 3 |
| Model proposes / code disposes; tools; identity; human gates; budgets; kill switch | 4 |
| AIBOM/ML-BOM; MCP host/client/server; pin/sign/allowlist; supply-chain plant path | 5 |
| Authorized adversarial eval; trustworthiness; metrics; log-based pass/fail; ATLAS tags | 6 |
| Shadow AI incident paths; data tiers; approved alternatives; DLP; private deploy | 7 |
| Jailbreak vs injection vs ASI01; OWASP #1–#9 anatomies; ATLAS IDs; Unicode; MCP poison/rug-pull; fixtures | 8 |

## Related homes (do not duplicate)

| Topic | Home |
|-------|------|
| AI-era threat literacy | [Threats/8](../Threats/8_AI_Era_Threats.md) |
| AI / agentic threat modeling (workshops) | [Threat-Modeling/6](../Threat-Modeling/6_AI_And_Agentic_Threat_Modeling.md) |
| Classic AppSec / OWASP web | [AppSec](../AppSec/README.md) |
| Supply chain (general) | [Cloud-Security/3](../Cloud-Security/3_Supply_Chain.md) |
| Human / social engineering | [Foundations/16](../Foundations/16_Human_Security_Literacy.md) |
| ATT&CK / ATLAS nesting | [GRC/1](../GRC/1_NIST_ISO_And_ATTCK.md) · [atlas.mitre.org](https://atlas.mitre.org/) |
| CTI handoff | [Threat-Intelligence](../Threat-Intelligence/README.md) |
