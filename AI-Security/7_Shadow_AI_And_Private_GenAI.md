# Shadow AI governance and private GenAI deployment

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to govern **unsanctioned GenAI use** and when to offer **private / approved** alternatives. Literacy: [Threats/8](../Threats/8_AI_Era_Threats.md). Build controls for approved systems: notes [2](./2_Prompt_Injection_Defenses.md)–[5](./5_Model_And_Data_Supply_Chain.md). Eval of what you build: [6](./6_Authorized_AI_Evaluation.md).

## Concept

### What shadow AI is

**Shadow AI:** staff use public or unapproved GenAI tools with corporate (or sensitive personal) data — no inventory, no DPA clarity, no logging, no model/data residency control.

| Why it appears | Why security cares |
|----------------|--------------------|
| Speed / convenience | Prompt/data leakage to third parties |
| Missing approved tools | Unmanaged plugins/MCP on laptops |
| Unclear policy | Regulated data in consumer chatbots |
| “It’s just a draft” | Still training/retention risk per vendor terms |
| Agents on personal devices | Unreviewed MCP servers with corp tokens |

Blocking without an **approved path** usually drives more shadow use. Governance = **policy + approved alternatives + detection + education**, then enforcement.

### How shadow AI becomes a real incident

Walk these paths when you brief leadership — they are how “someone pasted a doc” turns into a register item:

1. **Exfil via prompt** — Engineer pastes source, customer export, or keys into a consumer chatbot. Vendor retention/training terms may keep residual copies outside your DLP reach.
2. **Unmanaged agent** — Laptop installs a community MCP or IDE plugin that holds a corp OAuth token; ticket text or web content steers tool calls ([8](./8_Attack_Mechanisms_For_Defenders.md)).
3. **Decision fraud** — Staff trust fluent AI output for payments, access, or identity verification without a verify-before-act process (synthetic media / social engineering).
4. **Shadow RAG** — Personal tools index shared drives without ACL-before-retrieve ([3](./3_RAG_And_Memory_Hardening.md)).

```text
No approved tool
  → staff use consumer GenAI / random MCP
    → sensitive data or tokens leave the boundary
      → no logs, no kill switch, no AIBOM
```

### Governance outcomes

| Outcome | Meaning |
|---------|---------|
| Inventory | Which AI tools/models are in use (official + discovered) |
| Classification rules | What data may go to which tier of AI service |
| Approved catalog | Private/enterprise GenAI, vetted SaaS, banned classes |
| Contracts / DPAs | Retention, training-on-your-data opt-out, subprocessors |
| Monitoring | DLP / proxy / CASB signals for GenAI destinations |
| Exception process | Time-bounded waivers with owner and compensating controls |

## Advanced

### Data tiers → AI destinations (example policy shape)

| Data tier | Allowed AI use (illustrative) |
|-----------|-------------------------------|
| Public | Approved public tools OK |
| Internal | Enterprise GenAI / private deploy only |
| Confidential / regulated | Private deploy or prohibited; legal review |
| Secrets / credentials | **Never** paste into any GenAI |

Tune to your classification scheme ([GRC](../GRC/README.md) / org policy). Publish the table where people work — not only in a PDF nobody opens.

### Private / enterprise GenAI patterns

| Pattern | When | Still required |
|---------|------|----------------|
| **Vendor enterprise tenant** | SSO, admin controls, no-train clauses, regional residency | Logging, DLP, acceptable use |
| **Private VPC / on-prem model serving** | Higher isolation; you own patching/GPU/ops ([5](./5_Model_And_Data_Supply_Chain.md)) | Injection, agency, AIBOM, eval |
| **Grounded RAG on internal corpus** | Prefer over pasting secrets into chat | ACL-before-retrieve ([3](./3_RAG_And_Memory_Hardening.md)) |
| **Agentic only on low-risk tasks** | Careful adoption | Human gates, budgets, kill switch ([4](./4_Agentic_Controls.md)) |

**Private ≠ automatically safe.** Moving inference inside the perimeter removes some vendor retention risk and creates the full AppSec + AI-security surface this folder covers.

### Program checklist themes

| Theme | Practice hook |
|-------|----------------|
| Know your AI use | Inventory + shadow discovery |
| Protect against synthetic media / fraud | Verify-before-pay / verify-before-trust processes |
| Secure AI systems you build | This whole folder |
| Supply chain | Note [5](./5_Model_And_Data_Supply_Chain.md) |
| Human factors | Training; approval UX; social engineering via fluent AI |
| Critical infrastructure dependencies | Know where AI sits in ops; prepare for AI-enabled threats |

### Detection of shadow AI (without boiling the ocean)

| Signal | Action |
|--------|--------|
| DNS/proxy to known consumer GenAI | Coach + offer approved tool; escalate repeat offenders with regulated data |
| Browser extensions / MCP installs | Endpoint allowlists; remove unknown servers |
| Code assistants with unknown exfil | DevSec standards in IDE; pin approved assistants |
| Pasted secrets in tickets/chat | DLP rules + rotation |
| SaaS OAuth grants to AI apps | Identity review; revoke unused |

Coordinate with Defensive-Ops for SIEM; Identity for SSO to approved apps only.

### Rollout sequence that works

```text
1. Policy + data tiers (clear, short)
2. Approved enterprise/private offering (usable — latency and UX matter)
3. Training + champions
4. Discovery / DLP (measure shadow)
5. Exceptions with expiry
6. Enforce (block high-risk destinations)
```

Enforce before offering an alternative → underground workarounds. Offer without enforce → infinite shadow. Sequence both.

### Tie-back to systems you build

Shadow AI governance is **Govern**. Systems you then approve still need Map → Measure → Manage ([1](./1_LLM_App_Threat_Model_In_Practice.md), [6](./6_Authorized_AI_Evaluation.md)). Do not declare victory because SSO landed on a vendor logo.

## Apply

1. Draft a one-page GenAI acceptable-use policy with data tiers.  
2. List current approved vs unapproved tools in your org (honest inventory).  
3. Design the “approved alternative” for the top shadow use case — make it good enough to win.  
4. Write three DLP/proxy rules (destinations + secret patterns).  
5. Define exception form fields (owner, data types, expiry, compensating controls).  
6. Brief leadership: shadow AI risk paths + private GenAI investment in five bullets.  
7. Link this program to AI RMF **Govern** and your threat register for built systems.  
8. Add endpoint/MCP allowlist review to onboarding for engineering roles.

### Self-check

- [ ] Define shadow AI and why bans-alone fail  
- [ ] Narrate at least two incident paths (prompt exfil; unmanaged MCP)  
- [ ] Map data tiers to AI destinations  
- [ ] Contrast enterprise tenant vs private deploy vs grounded RAG  
- [ ] List discovery signals for unsanctioned use  
- [ ] State a sane rollout order (offer then enforce)  
- [ ] State why private deploy still needs notes 2–6  

## Further reading

- [CCCS — Top 10 artificial intelligence security actions (ITSAP.10.049)](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049)
- [CCCS — Generative AI (ITSAP.00.041)](https://www.cyber.gc.ca/en/guidance/generative-artificial-intelligence-ai-itsap00041)
- [CCCS / Five Eyes — Careful adoption of agentic AI](https://www.cyber.gc.ca/en/guidance/careful-adoption-agentic-ai)
- [DHS — AI safety and security guidelines for CI owners/operators](https://www.dhs.gov/sites/default/files/2024-04/24_0426_dhs_ai-ci-safety-security-guidelines-508c.pdf)
- [CISA — Software must be secure by design, and AI is no exception](https://www.cisa.gov/news-events/news/software-must-be-secure-design-and-artificial-intelligence-no-exception)
- [Threats/8 — AI-era threats](../Threats/8_AI_Era_Threats.md)
