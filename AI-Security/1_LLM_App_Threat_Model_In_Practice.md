# LLM application threat model in practice

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to **run** threat modeling for LLM apps and agents as an ongoing build/security practice — not a one-off slide. Workshop method and catalog tables: [Threat-Modeling/6](../Threat-Modeling/6_AI_And_Agentic_Threat_Modeling.md). Threat literacy: [Threats/8](../Threats/8_AI_Era_Threats.md). Defenses land in notes [2](./2_Prompt_Injection_Defenses.md)–[5](./5_Model_And_Data_Supply_Chain.md).

No jailbreak recipes. Authorized testing process: [6](./6_Authorized_AI_Evaluation.md).

## Concept

### What “practice depth” adds beyond TM/6

| TM/6 (workshop) | This note (operate) |
|-----------------|---------------------|
| Elicit threats with LLM/ASI/MAESTRO | Own a living threat register tied to **DFD + tickets** |
| Pick component vs actor checklists | Gate **releases** and tool grants on checklist outcomes |
| Map ATLAS for realism | Feed **detection / eval** ([6](./6_Authorized_AI_Evaluation.md)) and CTI ([Threat-Intelligence](../Threat-Intelligence/README.md)) |

### Component vs agent (decide before catalogs)

| Framing | System shape | Primary catalogs |
|---------|--------------|------------------|
| **Model as component** | Chat, summarize, classify, RAG Q&A with limited/no tools | OWASP **LLM Top 10 (2026)** + STRIDE |
| **Model as actor (agentic)** | Plans, tools, memory, multi-agent, side effects | LLM Top 10 **plus** OWASP **ASI Top 10 (2026)** + MAESTRO layers |

CCCS / Five Eyes caution (program): prefer **low-risk, non-sensitive** agentic tasks; never grant broad unrestricted access; human gates for high-impact actions; AI controls **add to** patching, identity, logging — they do not replace them.

### Minimum DFD elements (must appear on the diagram)

| Element | Why threats land here |
|---------|------------------------|
| User / untrusted content channels | Email, web, uploads, images/audio — injection entry |
| App / orchestrator | Policy home; must not trust the model |
| System / developer prompts | Privileged instructions (LLM08) |
| Model / inference API | Process or external service |
| RAG / vector / embeddings | Store + retrieve path (LLM09) |
| Tools / MCP servers | Side effects (LLM03 / ASI02–05) |
| Memory / session store | Persistence (ASI06) |
| Human approval gates | Automation bias (ASI09) |
| Supply chain (models, packages, MCP) | LLM04 / ASI04 |

```text
Untrusted content (user, RAG, tools, memory)
        │
        ▼
┌──────────────┐    ┌─────────────┐    ┌────────────────┐
│ Orchestrator │───▶│ LLM + prompts│───▶│ Tools / MCP    │
│ (policy)     │◀───│             │    │ (side effects) │
└──────┬───────┘    └──────▲──────┘    └────────────────┘
       │                   │
       ▼                   │
  Memory / RAG ────────────┘
```

**Lethal trifecta check (OWASP LLM01 narrative):** if an agent can **(1)** access private data, **(2)** ingest untrusted content, and **(3)** communicate externally — high-impact exploitation conditions exist. Remove or tightly gate at least one leg before production.

## Advanced

### OWASP LLM Top 10 (2026) — control ownership map

Use as a **release checklist** against your diagram (IDs from OWASP GenAI LLM Top 10 2026):

| ID | Risk | Primary control owners | Deepen in |
|----|------|------------------------|-----------|
| **LLM01** | Prompt Injection | AppSec + platform — assume bypass; constrain blast radius | [2](./2_Prompt_Injection_Defenses.md) |
| **LLM02** | Sensitive Information Disclosure | Privacy + AppSec — minimize context; redaction; no secrets in prompts | [3](./3_RAG_And_Memory_Hardening.md) |
| **LLM03** | Excessive Agency | Platform + IAM — least privilege tools; human gates | [4](./4_Agentic_Controls.md) |
| **LLM04** | Supply Chain | SecEng + procurement — pin/sign/attest models & tools | [5](./5_Model_And_Data_Supply_Chain.md) |
| **LLM05** | Data and Model Poisoning | Data + MLOps — ingest integrity; fine-tune controls | [3](./3_RAG_And_Memory_Hardening.md), [5](./5_Model_And_Data_Supply_Chain.md) |
| **LLM06** | Unbounded Consumption | SRE + FinOps — quotas, rate limits, cost caps | [4](./4_Agentic_Controls.md) |
| **LLM07** | Misinformation | Product + risk — grounding, citations, human decision rules | [3](./3_RAG_And_Memory_Hardening.md), [6](./6_Authorized_AI_Evaluation.md) |
| **LLM08** | Hidden Context Exposure | AppSec — protect system prompts, tool schemas, private retrieval | [2](./2_Prompt_Injection_Defenses.md) |
| **LLM09** | Vector and Embedding Weaknesses | Data platform — ACL’d chunks, tenant isolation | [3](./3_RAG_And_Memory_Hardening.md) |
| **LLM10** | Improper Output Handling | AppSec — treat model output as untrusted (XSS/SQLi/shell) | [2](./2_Prompt_Injection_Defenses.md) |

2026 ranking signal: **Excessive Agency** rose (damage lands in tools); **Unbounded Consumption** rose (cost/availability). Do not stop at “we filtered prompts.”

### ASI Top 10 (2026) — when the model acts

| ID | Risk | Practice gate |
|----|------|---------------|
| **ASI01** | Agent Goal Hijack | Multi-step plan review; untrusted content cannot rewrite goals silently |
| **ASI02** | Tool Misuse | Per-tool authZ; dry-run / human confirm for destructive actions |
| **ASI03** | Identity & Privilege Abuse | Non-human identities; no inherited god-tokens |
| **ASI04** | Agentic Supply Chain | MCP/tool registry allowlists; AIBOM | 
| **ASI05** | Unexpected Code Execution | No arbitrary code from model text; sandboxed runners |
| **ASI06** | Memory & Context Poisoning | Memory write ACL; purge/rollback |
| **ASI07** | Insecure Inter-Agent Comms | Authn/integrity on A2A |
| **ASI08** | Cascading Failures | Blast-radius limits; circuit breakers |
| **ASI09** | Human-Agent Trust Exploitation | Approval UX that shows risk, not just fluent prose |
| **ASI10** | Rogue Agents | Drift monitoring; kill switch ([4](./4_Agentic_Controls.md)) |

### NIST AI RMF + CISA/NCSC lifecycle (program spine)

| Framework | How to use in this folder |
|-----------|---------------------------|
| **NIST AI RMF** (Govern / Map / Measure / Manage) | Threat model = **Map**; eval = **Measure**; tickets/controls = **Manage**; policies = **Govern** |
| **NIST AI 600-1** GenAI Profile | GenAI-specific risk profile alongside RMF |
| **CISA/NCSC Guidelines for secure AI system development** | Secure **design → development → deployment → operation** checklist for providers of AI systems |

### Living threat register (minimum fields)

| Field | Example |
|-------|---------|
| DFD element | `rag-store`, `mcp:email-send` |
| Catalog ID | LLM01 / ASI02 / ATLAS technique |
| Scenario | Indirect injection via retrieved PDF → tool call |
| Impact | Exfil / money / integrity |
| Control | Deterministic policy engine; human gate |
| Ticket / status | SEC-1234 Mitigated |
| Eval coverage | Red-team case ID ([6](./6_Authorized_AI_Evaluation.md)) |

## Apply

1. Classify one real or planned system as **component** vs **agent**; list which catalogs apply.  
2. Draw the minimum DFD elements; mark the lethal-trifecta legs present/absent.  
3. Walk LLM01–LLM10 against the diagram; open a ticket for every “no control.”  
4. If agentic, walk ASI01–ASI10; assign owners.  
5. Map five threats to AI RMF Map → Measure → Manage actions.  
6. Write a release gate: “No new tool grant without …” (three bullets).

### Self-check

- [ ] Distinguish component vs agent framing and catalogs  
- [ ] Name the lethal trifecta and why it matters  
- [ ] List minimum DFD elements for LLM apps  
- [ ] Recite LLM Top 10 IDs at headline level with a control owner each  
- [ ] Know when ASI Top 10 is mandatory  
- [ ] Place RMF and CISA/NCSC lifecycle in the program  

## Next

- [Prompt injection defenses](./2_Prompt_Injection_Defenses.md)

## Further reading

- [OWASP Top 10 for LLM Applications 2026](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/)
- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [Threat-Modeling/6 — AI and agentic threat modeling](../Threat-Modeling/6_AI_And_Agentic_Threat_Modeling.md)
- [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST AI 600-1 — Generative AI Profile](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)
- [CISA / NCSC — Guidelines for secure AI system development](https://www.cisa.gov/resources-tools/resources/guidelines-secure-ai-system-development)
- [MITRE ATLAS](https://atlas.mitre.org/)
