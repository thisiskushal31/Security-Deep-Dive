# Shadow AI governance and private GenAI deployment

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to govern **unsanctioned GenAI use** and when to offer **private / approved** alternatives. Literacy: [Threats/8](../Threats/8_AI_Era_Threats.md). Program actions: [CCCS Top 10 AI security actions](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049) · [CCCS Generative AI](https://www.cyber.gc.ca/en/guidance/generative-artificial-intelligence-ai-itsap00041). Critical infrastructure framing: [DHS AI CI guidelines](https://www.dhs.gov/sites/default/files/2024-04/24_0426_dhs_ai-ci-safety-security-guidelines-508c.pdf). Build controls: notes [2](./2_Prompt_Injection_Defenses.md)–[5](./5_Model_And_Data_Supply_Chain.md).

## Concept

### What shadow AI is

**Shadow AI:** staff use public or unapproved GenAI tools with corporate (or sensitive personal) data — no inventory, no DPA clarity, no logging, no model/data residency control.

| Why it appears | Why security cares |
|----------------|--------------------|
| Speed / convenience | Prompt/data leakage to third parties |
| Missing approved tools | Unmanaged plugins/MCP on laptops |
| Unclear policy | Regulated data in consumer chatbots |
| “It’s just a draft” | Still training/retention risk per vendor terms |

Blocking without an **approved path** usually drives more shadow use. Governance = **policy + approved alternatives + detection + education**.

### Governance outcomes

| Outcome | Meaning |
|---------|---------|
| Inventory | Which AI tools/models are in use (official + discovered) |
| Classification rules | What data may go to which tier of AI service |
| Approved catalog | Private/enterprise GenAI, vetted SaaS, banned classes |
| Contracts / DPAs | Retention, training-on-your-data opt-out, subprocessors |
| Monitoring | DLP / proxy / CASB signals for GenAI destinations |
| Exception process | Time-bounded waivers with owner |

## Advanced

### Data tiers → AI destinations (example policy shape)

| Data tier | Allowed AI use (illustrative) |
|-----------|-------------------------------|
| Public | Approved public tools OK |
| Internal | Enterprise GenAI / private deploy only |
| Confidential / regulated | Private deploy or prohibited; legal review |
| Secrets / credentials | **Never** paste into any GenAI |

Tune to your classification scheme ([GRC](../GRC/README.md) / org policy).

### Private / enterprise GenAI patterns

| Pattern | When |
|---------|------|
| **Vendor enterprise tenant** | SSO, admin controls, no-train clauses, regional residency |
| **Private VPC / on-prem model serving** | Higher isolation; you own patching/GPU/ops ([5](./5_Model_And_Data_Supply_Chain.md)) |
| **Grounded RAG on internal corpus** | Prefer over pasting secrets into chat — with [3](./3_RAG_And_Memory_Hardening.md) controls |
| **Agentic only on low-risk tasks** | CCCS/Five Eyes careful adoption ([4](./4_Agentic_Controls.md)) |

Private ≠ automatically safe: still need injection defenses, agency limits, SBOM/AIBOM, logging, and eval ([6](./6_Authorized_AI_Evaluation.md)).

### CCCS-aligned action themes (program checklist)

Use CCCS Top 10 AI security actions as a **board-readable** checklist (pair with Threats/8 literacy):

| Theme | Practice hook |
|-------|----------------|
| Know your AI use | Inventory + shadow discovery |
| Protect against synthetic media / fraud | Process for verify-before-pay / verify-before-trust |
| Secure AI systems you build | This whole folder |
| Supply chain | Note [5](./5_Model_And_Data_Supply_Chain.md) |
| Human factors | Training; approval UX; social engineering via fluent AI |

### Detection of shadow AI (without boiling the ocean)

| Signal | Action |
|--------|--------|
| DNS/proxy to known consumer GenAI | Coach + offer approved tool |
| Browser extensions / MCP installs | Endpoint allowlists |
| Code assistants with unknown exfil | DevSec standards in IDE |
| Pasted secrets in tickets/chat | DLP rules + rotation |

Coordinate with Defensive-Ops for SIEM; Identity for SSO to approved apps only.

### DHS / critical infrastructure note

If you support CI owners/operators: DHS AI safety/security guidelines emphasize understanding AI dependencies, securing AI-enabled systems, and preparing for AI-enabled threats (deepfakes, faster attacks). Shadow AI in CI contexts is a **governance and resilience** issue, not only an Acceptable Use footnote.

### Rollout sequence that works

```text
1. Policy + data tiers (clear)
2. Approved enterprise/private offering (usable)
3. Training + champions
4. Discovery / DLP (measure shadow)
5. Exceptions with expiry
6. Enforce (block high-risk destinations)
```

Enforce before offering an alternative → underground workarounds.

## Apply

1. Draft a one-page GenAI acceptable-use policy with data tiers.  
2. List current approved vs unapproved tools in your org (honest inventory).  
3. Design the “approved alternative” for the top shadow use case.  
4. Write three DLP/proxy rules (destinations + secret patterns).  
5. Define exception form fields (owner, data types, expiry, compensating controls).  
6. Brief leadership: shadow AI risk + private GenAI investment in five bullets.  
7. Link this program to AI RMF **Govern** and your threat register for built systems ([1](./1_LLM_App_Threat_Model_In_Practice.md)).

### Self-check

- [ ] Define shadow AI and why bans-alone fail  
- [ ] Map data tiers to AI destinations  
- [ ] Contrast enterprise tenant vs private deploy vs grounded RAG  
- [ ] List discovery signals for unsanctioned use  
- [ ] State a sane rollout order (offer then enforce)  
- [ ] Tie CCCS/DHS themes to program actions  

## Further reading

- [CCCS — Top 10 artificial intelligence security actions (ITSAP.10.049)](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049)
- [CCCS — Generative AI (ITSAP.00.041)](https://www.cyber.gc.ca/en/guidance/generative-artificial-intelligence-ai-itsap00041)
- [CCCS / Five Eyes — Careful adoption of agentic AI](https://www.cyber.gc.ca/en/guidance/careful-adoption-agentic-ai)
- [DHS — AI safety and security guidelines for CI owners/operators](https://www.dhs.gov/sites/default/files/2024-04/24_0426_dhs_ai-ci-safety-security-guidelines-508c.pdf)
- [CISA — Software must be secure by design, and AI is no exception](https://www.cisa.gov/news-events/news/software-must-be-secure-design-and-artificial-intelligence-no-exception)
- [Threats/8 — AI-era threats](../Threats/8_AI_Era_Threats.md)
