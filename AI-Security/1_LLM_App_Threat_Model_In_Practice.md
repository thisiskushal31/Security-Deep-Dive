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

Program floor for agents: prefer **low-risk, non-sensitive** agentic tasks; never grant broad unrestricted access; human gates for high-impact actions; AI controls **add to** patching, identity, logging — they do not replace them.

### How practice threat modeling prevents the usual miss

Teams that “did a workshop once” still ship the lethal trifecta because ownership never landed on a living register. Practice depth means:

1. **Draw** the DFD with every untrusted content path and every tool principal.  
2. **Walk** LLM (and ASI if agentic) catalogs against that diagram — not against a slide template.  
3. **Record** each gap as a register row with ticket + eval coverage.  
4. **Gate** releases and tool grants on open critical rows.  
5. **Re-walk** when you add MCP, a new retrieve source, or a new autonomy tier.

If step 4 is missing, Map never becomes Manage.

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

**Lethal trifecta / Rule of Two check:** if an agent has **(A)** untrusted input, **(B)** sensitive data, and **(C)** state-change or external communication together — high-impact exploitation conditions exist. `[A,B,C]` needs per-action human approval; `[A,B]` / `[A,C]` need residual-risk acceptance; removing any leg collapses the high-impact case. How that chain plays out: [8](./8_Attack_Mechanisms_For_Defenders.md).

Program risk work sits in four continuous functions — govern the culture, map context and risks, measure them, manage treatments:

![NIST AI RMF Core — GOVERN, MAP, MEASURE, MANAGE](../Assets/AI-Security/NIST_AI_RMF_Fig5_Core_Functions.png)

*Credit: NIST AI RMF 1.0 (AI 100-1), Figure 5.*

```text
Govern (policy, roles, accountability)
  → Map (DFD + catalogs + register)     ← this note
    → Measure (eval, metrics)           ← [6](./6_Authorized_AI_Evaluation.md)
      → Manage (tickets, controls, accept residual risk)
```

## Advanced

### LLM Top 10 (2026) — control ownership map

Use as a **release checklist** against your diagram:

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

### Agentic Top 10 (2026) — when the model acts

When the system plans, calls tools, keeps memory, or talks to other agents, walk these after (or with) the LLM list:

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

### Who must be in the room across the lifecycle

Threat modeling is not only “security week.” Actors change by stage — planners, data people, model builders, validators, operators, and people impacted by the system:

![NIST AI RMF — AI actors across lifecycle stages](../Assets/AI-Security/NIST_AI_RMF_Fig3_Actors_Lifecycle.png)

*Credit: NIST AI RMF 1.0 (AI 100-1), Figure 3.*

Separate **builders** of models from **validators** of models when you can — so production pressure does not rubber-stamp testing, evaluation, verification, and validation (TEVV).

### Secure AI lifecycle checklist (providers)

For systems you build or deeply configure, drive Map/Measure/Manage through four engineering stages:

| Stage | Threat-model questions |
|-------|------------------------|
| **Design** | Where is untrusted content? Which tools? Which identities? Trifecta? |
| **Development** | Are prompts/tools reviewed like code? Dependencies pinned? |
| **Deployment** | Signed artifacts? Secrets out of images? Kill switch wired? |
| **Operation** | Logging, drift, eval cadence, incident paths for AI failures? |

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
| Residual risk | Accepted by whom / until when (if not mitigated) |

### Release and tool-grant gates (examples)

| Gate | Block if |
|------|----------|
| New MCP / tool | No owner, no risk tier, no policy rules, not in AIBOM |
| New retrieve source | No ACL metadata plan, no ingest provenance |
| Raise autonomy (fewer human gates) | Open critical LLM03/ASI02 rows; no kill-switch drill in last quarter |
| Production agent with trifecta | No explicit residual-risk acceptance + per-action approval design |

## Apply

1. Classify one real or planned system as **component** vs **agent**; list which catalogs apply.  
2. Draw the minimum DFD elements; mark the lethal-trifecta legs present/absent.  
3. Walk LLM01–LLM10 against the diagram; open a ticket for every “no control.”  
4. If agentic, walk ASI01–ASI10; assign owners.  
5. Map five threats to Map → Measure → Manage actions.  
6. Write a release gate: “No new tool grant without …” (three bullets).  
7. Add eval coverage IDs for the top three register rows ([6](./6_Authorized_AI_Evaluation.md)).  
8. Mark which actors from Fig. 3 must sign the next agent go-live.

### Self-check

- [ ] Distinguish component vs agent framing and catalogs  
- [ ] Name the lethal trifecta and why it matters  
- [ ] List minimum DFD elements for LLM apps  
- [ ] Recite LLM Top 10 IDs at headline level with a control owner each  
- [ ] Know when the agentic Top 10 is mandatory  
- [ ] Place Govern / Map / Measure / Manage on your program  
- [ ] Name design → development → deployment → operation questions  
- [ ] Define a tool-grant gate that can actually block a release  

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
