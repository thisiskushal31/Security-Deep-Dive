# AI and agentic threat modeling

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

How to threat-model systems that include **LLMs, RAG, tools, memory, and agents** — without abandoning the four questions or DFD/STRIDE. Threat *literacy* (what AI-era threats are): [Threats/8](../Threats/8_AI_Era_Threats.md). Build/operate depth: [AI-Security](../AI-Security/README.md). Classical method: [1](./1_Workshop_Process.md)–[5](./5_Tooling_And_Closing_The_Loop.md).

No jailbreak recipes, red-team payloads, or misuse enablement — definitions, elicitation checklists, and program framing only ([Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md)).

## Concept

AI does not replace threat modeling. It **widens the diagram** and adds threat catalogs that STRIDE alone does not name well. Practice order:

```text
Four questions + DFD + STRIDE (notes 1–2)
  → AI-shaped elements on the diagram (model, RAG, tools, memory, agents)
    → LLM Top 10 (model as component) and/or ASI Top 10 (model as actor)
      → MAESTRO layer pass when agentic / multi-agent
        → ATLAS (and ATT&CK) for TTP realism and case studies
          → Mitigate / accept / ticket / validate (note 5)
```

| Framing | Use it for |
|---------|------------|
| **DHS / CISA three categories** | Scope the *picture*: attacks **using** AI · attacks **targeting** AI · **failures** in AI design/implementation — [Threats/8](../Threats/8_AI_Era_Threats.md) |
| **STRIDE-per-element** | Classical trust-boundary issues still present (spoofed callers, tampered tool args, disclosure of context, DoS on inference, elevation via tools) |
| **OWASP LLM Top 10** | LLM *applications* — prompt/RAG/output/supply-chain risks |
| **OWASP ASI Top 10** | *Agentic* apps — goals, tools, identity, memory, multi-agent, cascades |
| **CSA MAESTRO** | Layered decomposition of agent stacks + cross-layer cascades |
| **MITRE ATLAS** | Living TTP knowledge base for AI (like ATT&CK for AI-shaped campaigns) |

### Component vs actor (pick the right checklist)

| Role of the model | Typical surface | Primary elicitation |
|-------------------|-----------------|---------------------|
| **Component** | Chat UI, summarizer, classifier, RAG Q&A with limited tools | OWASP **LLM** Top 10 + STRIDE on host/API/stores |
| **Actor (agentic)** | Plans steps, invokes tools, keeps memory, calls other agents, acts on systems | LLM Top 10 **plus** OWASP **ASI** Top 10 + MAESTRO layers |

CCCS / Five Eyes program caution (literacy): prefer **low-risk, non-sensitive** agentic tasks; never grant broad unrestricted access; keep human gates for high-impact decisions; AI controls **add to** classic hygiene (patching, identity, logging), they do not replace it.

### What STRIDE still covers — and what it misses

| Still covered well | Weak alone (augment) |
|--------------------|----------------------|
| Spoofed users/APIs/agents as interactors | Adversarial ML / evasion on the model |
| Tampering with prompts, retrieved docs, tool I/O, configs | Training/fine-tune/RAG **poisoning** as first-class threats |
| Disclosure of PII, secrets, system prompts, embeddings | **Goal hijack** and multi-step planning abuse |
| DoS / cost exhaustion on inference or vector DB | **Cascading** multi-agent failure modes |
| Elevation via over-privileged tools / confused deputy | Collusion, sybil, insecure **A2A** protocols |

CSA’s MAESTRO comparison is blunt: treat STRIDE as a **starting point**, then add AI-specific categories and layered agent architecture — do not invent new STRIDE letters and pretend the gaps closed.

## Advanced

### A. Extend the DFD before you open AI catalogs

Add elements (or label existing ones) so threats have somewhere to land:

| Element / label | Why it matters |
|-----------------|----------------|
| **Model / inference API** | Process or external store — who can call it; what context it sees |
| **System / developer prompts** | Sensitive *instructions* — treat as privileged config, not “just text” |
| **RAG / vector store / embeddings** | Store + flows: ingest → embed → retrieve → inject into context |
| **Tools / plugins / MCP servers** | Processes with real side effects (mail, code, cloud APIs) |
| **Agent memory / session store** | Persistence across turns — poisoning lasts longer than one prompt |
| **Orchestrator / planner** | Process that chooses tools and next steps |
| **Other agents (A2A)** | Interactors or processes with their own trust boundaries |
| **Human approval gates** | Interactors that can be socially engineered by fluent output |
| **Training / fine-tune / eval pipelines** | Data flows that can poison behavior before production |
| **Model/artifact supply chain** | External interactors: hubs, registries, base images, MCP packages |

Trust-boundary habits from [2](./2_DFD_And_STRIDE_In_Practice.md) still apply: clients and retrieved content are **untrusted**; “the model said so” is not a trust boundary.

```text
User / untrusted content
        │
        ▼
┌───────────────┐     ┌─────────────┐     ┌───────────────┐
│ App / agent   │────▶│ LLM / model │────▶│ Tools / APIs  │
│ orchestrator  │◀────│ + prompts   │     │ (side effects)│
└───────┬───────┘     └──────▲──────┘     └───────────────┘
        │                    │
        ▼                    │
   Memory / RAG ◀────────────┘
   (retrieved text is attacker-shaped input)
```

### B. Workshop elicitation — LLM Top 10 (model as component)

Use the official **OWASP Top 10 for LLM Applications (2026)** as a *checklist against your diagram*, not a substitute for it:

| ID | Risk | Threat-model prompt |
|----|------|---------------------|
| **LLM01** | Prompt Injection | Where can untrusted text/media enter context (user, email, web, RAG, tool output, memory)? |
| **LLM02** | Sensitive Information Disclosure | What secrets/PII can the model see or emit? |
| **LLM03** | Excessive Agency | What actions can the app take without a human? Least privilege on tools? |
| **LLM04** | Supply Chain | Models, datasets, plugins, training pipelines, third-party components? |
| **LLM05** | Data and Model Poisoning | Who can influence train/fine-tune/embed/ingest data? |
| **LLM06** | Unbounded Consumption | Quotas, cost caps, expensive queries, sponge/DoS patterns? |
| **LLM07** | Misinformation | Who acts on fluent wrong answers (humans or automated tools)? |
| **LLM08** | Hidden Context Exposure | System prompts, tool schemas, private retrieval, or other privileged context leaking? |
| **LLM09** | Vector and Embedding Weaknesses | RAG isolation, access control, poisoned/retrieved chunks? |
| **LLM10** | Improper Output Handling | Where does model output become HTML, SQL, shell, or trusted config? |

2026 ranking shifts that matter in the room: **Excessive Agency** climbed (agent damage is where impact lands); **Unbounded Consumption** rose (cost + availability); former “System Prompt Leakage” broadened to **Hidden Context Exposure**. Do not stop at “prompt injection” alone.

### C. Workshop elicitation — ASI Top 10 (model as actor)

When the system **plans, uses tools, keeps memory, or multi-agents**, walk **OWASP Top 10 for Agentic Applications (2026)** after (or interleaved with) LLM:

| ID | Risk | Threat-model prompt |
|----|------|---------------------|
| **ASI01** | Agent Goal Hijack | Can attacker-shaped content redirect goals/plans across *multiple* steps? |
| **ASI02** | Tool Misuse and Exploitation | Legitimate tools, unsafe use (wrong target, over-invoke, exfil via tool)? |
| **ASI03** | Identity and Privilege Abuse | Non-human identities, inherited credentials, confused deputy? |
| **ASI04** | Agentic Supply Chain | MCP/A2A descriptors, tool registries, agent packages, model hubs? |
| **ASI05** | Unexpected Code Execution | Can text become code/scripts on the host or in a sandbox escape? |
| **ASI06** | Memory & Context Poisoning | Long-lived memory/RAG that survives the original attack turn? |
| **ASI07** | Insecure Inter-Agent Communication | Spoofed/tampered agent messages; authn of A2A channels? |
| **ASI08** | Cascading Failures | One bad decision fan-out across agents/workflows? |
| **ASI09** | Human-Agent Trust Exploitation | Automation bias — fluent agent pushes harmful human approval? |
| **ASI10** | Rogue Agents | Behavioral drift / harmful autonomy after integrity loss? |

Useful distinctions from OWASP (keep them straight in the threat list):

| Confusion | Separation |
|-----------|------------|
| LLM01 vs ASI01 | Single-response steering vs **multi-step goal/plan** redirection |
| ASI02 vs ASI03 | Misusing *granted* tools vs abusing **identity/privilege** |
| ASI01 vs ASI06 vs ASI10 | Direct goal change vs **persistent memory** corruption vs **emergent** rogue behavior |
| ASI08 | Fan-out / amplification — tag the *origin* under ASI04/06/07 when that is the root |

### D. MAESTRO — layered pass for agent stacks

**MAESTRO** (Multi-Agent Environment, Security, Threat, Risk, and Outcome; CSA / Ken Huang) is a **seven-layer** reference architecture for agentic systems. Use it when the stack is more than “one chat API.”

| Layer | Focus | Example threat themes (CSA) |
|-------|--------|------------------------------|
| **7 Agent Ecosystem** | Marketplace / apps / users | Compromised or impersonated agents, goal manipulation, tool misuse, registry poisoning |
| **6 Security & Compliance** (vertical) | Controls across layers | Poisoned/evaded security agents, non-compliance, extraction of security models |
| **5 Evaluation & Observability** | Eval, metrics, monitoring | Metric manipulation, blind spots, leakage via logs/dashboards |
| **4 Deployment & Infrastructure** | Cloud/K8s/IaC | Compromised images, orchestration abuse, resource hijack, lateral movement |
| **3 Agent Frameworks** | SDKs, toolkits, orchestration libs | Compromised framework components, supply chain, weak input handling |
| **2 Data Operations** | DB, vector stores, RAG pipelines | Poisoning, exfil, tampering, compromised retrieval pipelines |
| **1 Foundation Models** | Base LLM / PredAI | Adversarial examples, model theft, backdoors, membership inference, train-time poison, DoS/sponge |

**MAESTRO method (six steps):**

1. **Decompose** the system onto the seven layers (capabilities, goals, interactions)  
2. **Layer-specific** threat pass (use the landscapes above; tailor to your design)  
3. **Cross-layer** threats (supply chain, lateral movement, privilege escalation, data leakage, **goal-misalignment cascades**)  
4. **Risk** — likelihood × impact in *this* agent’s context  
5. **Mitigate** — layer-specific + defense-in-depth + AI-specific (monitoring, human gates, least privilege, red team in authorized scope)  
6. **Implement and monitor** — living model; agents and tools change often

**Architecture patterns to ask about in the room** (each has a signature threat in CSA’s write-up): single-agent goal manipulation · multi-agent channel/identity attacks · unconstrained conversational autonomy (injection) · task-oriented API overload · hierarchical “compromise the boss agent” · distributed sybil/impersonation · human-in-the-loop feedback skew · self-learning backdoor/poison.

### E. ATLAS — after the diagram, for TTP realism

**MITRE ATLAS** (Adversarial Threat Landscape for AI Systems) is a living matrix of **tactics / techniques / mitigations / case studies** for AI — attacks against AI-enabled systems, abuse of AI capabilities, and harmful autonomous behavior enabled by AI. Use it the way you use ATT&CK after a DFD is stable ([Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md)):

| Habit | Practice |
|-------|----------|
| **When** | After Q1 diagram + LLM/ASI elicitation — deepen *how* an adversary would operate |
| **Platform tags** | Filter techniques by Predictive AI / Generative AI / Agentic AI / Enterprise |
| **Maturity** | Prefer **Realized** / **Demonstrated** case-backed techniques for prioritization theater |
| **Nesting** | Host/network steps → ATT&CK; AI-centered steps → ATLAS; many campaigns need **both** |
| **Close the loop** | Map chosen techniques to mitigations and owners ([5](./5_Tooling_And_Closing_The_Loop.md)) |

Tactics you will see on the official matrix (order as published): Reconnaissance · Resource Development · **AI Attack Adaptation** · Initial Access · **AI Model Access** · Execution · Persistence · Privilege Escalation · Defense Evasion · Credential Access · Discovery · Lateral Movement · Collection · Command and Control · Exfiltration · Impact. AI-specific flavors that often appear in agent models include prompt injection, RAG poisoning, agent tool invocation/poisoning, agent context poisoning, and exfiltration via tools or inference APIs — name them from ATLAS, do not invent private taxonomies.

### F. How PASTA and attack trees still help

| Method | AI-era use |
|--------|------------|
| **PASTA** ([3](./3_PASTA_Staged_Walkthrough.md)) | Stage I–II capture business harm of agent actions (fraud, data egress, unsafe automation). Stage IV–VI simulate attacker paths through RAG → goal hijack → tool misuse. Residual risk must include **autonomy** and **cost** abuse. |
| **Attack trees** ([4](./4_Attack_Trees_And_LINDDUN.md)) | Root: “exfil customer data via agent” or “unauthorized money movement.” AND/OR children: poison RAG · inject via email · abuse refund tool · bypass human gate. |
| **LINDDUN** ([4](./4_Attack_Trees_And_LINDDUN.md)) | Agents that process personal data — linkability, disclosure, unawareness; pair with LLM02 / memory stores. |

### G. Mitigation families that belong in Q3 (no payloads)

Write mitigations as **requirements and design choices**, then ticket them:

| Family | Examples of requirement language |
|--------|----------------------------------|
| **Trust & input** | Treat user, retrieved, email, and tool-returned text as untrusted; separate instructions from data where architecture allows |
| **Agency** | Least-privilege tools; deny-by-default tool allowlists; human approval for high-impact actions; kill switch / disable agent |
| **Identity** | Distinct non-human identities per agent/tool; no shared god-credentials; short-lived tokens |
| **Memory / RAG** | Access control on corpora; integrity of ingest; tenant isolation; purge/rotate poisoned memory |
| **Supply chain** | Pin/sign models and MCP packages; review tool descriptors; AIBOM/SBOM for AI deps |
| **Output handling** | Never execute or render model output as trusted code/HTML/SQL without validation appropriate to the sink |
| **Consumption** | Quotas, rate limits, max tool loops, cost alerts |
| **Observability** | Log tool calls, goal state, retrieval IDs; alert on anomalous fan-out (ASI08) |
| **Human factors** | Out-of-band verify money/access; train operators against automation bias (ASI09) |
| **Classic baseline** | Patch hosts, lock down CI/CD, segment networks — compromising the box often beats attacking the model |

## Apply

1. Take one LLM feature you own (or a public architecture sketch). Draw a DFD with model, prompts, RAG, tools, and memory. Mark trust boundaries.  
2. Run STRIDE-per-element for 20 minutes. List three threats STRIDE named cleanly and three it only hinted at.  
3. Walk **LLM01–LLM10** against that diagram; file concrete threats with element IDs.  
4. If any tool can change state in another system, also walk **ASI01–ASI10**; mark which ASI entries are N/A with a one-line reason.  
5. Map the system onto **MAESTRO** layers 1–7 (even if some layers are “N/A — managed SaaS”). Identify one **cross-layer** cascade.  
6. Pick the top three threats; for each write mitigate vs accept, owner, and a validation idea (test, gate, or monitoring).  
7. Open [ATLAS](https://atlas.mitre.org/), filter **Agentic AI** or **Generative AI**, and attach one realized/demonstrated technique to each of those top three.

### Self-check

- [ ] Explain component vs actor and when ASI is mandatory  
- [ ] Name five AI-shaped DFD elements beyond “web app + DB”  
- [ ] Recite three STRIDE gaps that need LLM/ASI/MAESTRO  
- [ ] List LLM Top 10 IDs in order (2026)  
- [ ] Contrast ASI01, ASI06, and ASI10 in one sentence each  
- [ ] Sketch MAESTRO layers 1–7 from memory  
- [ ] State when to open ATLAS vs ATT&CK  
- [ ] Give two mitigations that are *agency* controls, not “better prompts”  

## Next

Back to [Threat-Modeling README](./README.md) · threat literacy in [Threats/8](../Threats/8_AI_Era_Threats.md) · build depth in [AI-Security](../AI-Security/README.md) when filled · classical close-the-loop in [5](./5_Tooling_And_Closing_The_Loop.md).

## Further reading

- [CSA — Agentic AI Threat Modeling Framework: MAESTRO](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro)
- [OWASP Top 10 for LLM Applications (2026)](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/) · [LLM Top 10 hub](https://genai.owasp.org/llm-top-10/)
- [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [OWASP — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)
- [MITRE ATLAS](https://atlas.mitre.org/) · [ATLAS data (GitHub)](https://github.com/mitre-atlas/atlas-data)
- [NIST AI 600-1 — Generative AI Profile](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)
- [CISA / NCSC et al. — Guidelines for secure AI system development](https://www.cisa.gov/resources-tools/resources/guidelines-secure-ai-system-development)
- [CCCS — Top 10 artificial intelligence security actions](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049)
- [Threats/8 — AI-era threats (literacy)](../Threats/8_AI_Era_Threats.md)
