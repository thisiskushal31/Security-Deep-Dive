# Attack mechanisms for defenders

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How AI-era attacks **work**, so you can design controls, detections, and authorized tests. Defenses: [2](./2_Prompt_Injection_Defenses.md)–[5](./5_Model_And_Data_Supply_Chain.md). Eval process: [6](./6_Authorized_AI_Evaluation.md). Literacy: [Threats/8](../Threats/8_AI_Era_Threats.md). Technique catalog: [MITRE ATLAS](https://atlas.mitre.org/).

**Intent of this note:** teach *mechanisms and public incident patterns* (OWASP, ATLAS, NIST AML) for protection and **authorized** evaluation. It is not a jailbreak pack for bypassing production safety systems to cause harm. Practice only in scoped labs you own or are authorized to test ([Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md)).

## Concept

### Jailbreak vs prompt injection (keep these straight)

| Term | Meaning (ATLAS / industry) | Defender takeaway |
|------|----------------------------|-------------------|
| **Prompt injection** | Attacker-shaped input (user, doc, tool, image, …) steers model behavior for this app/session — ATLAS family around **LLM Prompt Injection** (e.g. AML.T0051 and variants) | Your **app boundary** failed; fix architecture ([2](./2_Prompt_Injection_Defenses.md)) |
| **Jailbreak** | Induce the model to ignore **safety/alignment guardrails** it was trained/policy-bound to follow — ATLAS **LLM Jailbreak** (e.g. AML.T0054) | Often about the **base model / vendor policy**; still pair with app containment because a jailbroken model + tools = impact |
| **Goal hijack (agentic)** | Multi-step plan/objective redirection — OWASP **ASI01** | Not “one funny reply”; the agent *keeps* working the wrong goal |

People say “jailbreak” colloquially for both. In a threat register, **split them**: injection into *your* orchestration vs bypass of *model* refusals vs agent goal drift.

### Why “knowing how it happens” matters for defense

```text
Attacker places instruction-shaped content somewhere the model will read
  → Model treats it like instructions (no hard instruction/data split)
    → If tools/agency exist → real-world impact (email, code, cloud, DB)
      → Controls that only “filter bad words” fail; blast-radius controls remain
```

OWASP LLM01:2026: no reliable prevention today; **assume bypass**; constrain capabilities.

### Anatomy template (use for every scenario)

| Field | Fill |
|-------|------|
| **Entry channel** | Direct chat · RAG/doc · email · web · image/audio · MCP/tool output · memory · supply-chain prompt |
| **Persistence** | Single-shot · cross-session · corpus-poison (every future retrieve) |
| **Encoding** | Plain text · split payload · invisible Unicode · steganographic / multimodal |
| **Impact path** | Chat-only · exfil · tool invoke · privilege abuse · cost DoS |
| **Catalog tags** | LLM01 / ASI0x / ATLAS ID |
| **Control that should have stopped it** | Policy engine · human gate · ACL · no tool · budget · kill switch |

## Advanced

### A. Mechanism classes (how, not a payload museum)

#### 1) Direct instruction override

**Idea:** user (or anyone on the chat path) pastes text that conflicts with the system role (“ignore your rules and …”).

| Defender lens | Control |
|---------------|---------|
| Expect it on every public chat API | Role constraint is weak alone |
| Impact only if agency/output sinks trust the model | Schema validate; no privileged tools; human gates |

**Authorized test shape (lab):** benign override attempts against a **dummy** policy (“only answer product FAQ”) and assert the **tool layer** still refuses `send_email` / `shell` even if the model’s text plays along.

#### 2) Indirect injection (retrieved / fetched content)

**Idea:** instructions live in a page, ticket, PDF, or email the assistant later reads under the **victim’s** privileges.

OWASP LLM01 scenarios (educational anatomies):

| Scenario (OWASP) | Anatomy |
|------------------|---------|
| Summarize web page → hidden instruction → markdown image URL exfil | Indirect web; single-shot; plain text in page source |
| Job-description PDF embeds AI-detection instruction; applicant’s LLM surfaces it | Unintentional / indirect PDF |
| Crafted email → productivity agent exfils data “zero click” | Email/doc; tools; sometimes invisible Unicode channel |
| Public GitHub issue / ticket / npm text → developer agent with elevated MCP | Trusted-surface indirect; multi-step tool chain |

**Defender lens:** treat every connector as an **untrusted instruction bus**. Remove a leg of the lethal trifecta (private data × untrusted content × external comms).

#### 3) Payload splitting / obfuscation

**Idea:** harmless-looking fragments combine into an instruction only after retrieval or multi-turn context (OWASP Scenario #5 class). Filters that match whole phrases miss splits. Related: encoding, low-resource languages, base64-ish wrappers — still **instruction smuggling**, not magic.

**Defender lens:** structural separation + capability limits beat deny-lists; strip invisible Unicode at boundaries (partial).

#### 4) Multimodal / steganographic injection

**Idea:** image/audio carries instruction below human notice; vision/ASR encoder surfaces it to the LLM (OWASP Scenario #6 class).

**Defender lens:** filter **every modality**; OCR/transcribe then apply text policy; don’t give multimodal agents high-privilege tools by default.

#### 5) RAG / memory poisoning

**Idea:** plant durable instructions or false facts in the corpus or long-term memory so **every future session** inherits them (LLM05/09, ASI06; OWASP Scenario #4 class).

**Defender lens:** ingest provenance, ACL-before-retrieve, memory write policy, purge ([3](./3_RAG_And_Memory_Hardening.md)).

#### 6) Jailbreak (guardrail bypass) — mechanism families

Jailbreaks aim at **model refusals** (harmful content, dual-use help, policy blocks). Families defenders should recognize in **authorized** testing (names, not a recipe pack):

| Family | Mechanism (high level) | Why apps still care |
|--------|------------------------|---------------------|
| **Role / privilege framing** | Ask the model to adopt a mode that “should” ignore policies | If your app trusts model text into tools, jailbreak ⇒ tool abuse |
| **Obfuscation / encoding** | Hide the request so naive filters miss it | Same as injection filters failing |
| **Multi-turn priming** | Slowly shift context until a refused ask slips through | Session memory / chat history is an attack surface |
| **Competing objectives** | Pit helpfulness against safety in the prompt | System prompts alone won’t hold |
| **Tool-assisted** | Jailbreak + then “call this API / run this” | LLM03/ASI02 — where damage lands |

**ATLAS:** track **LLM Jailbreak** separately from **Prompt Injection** in the register. Vendor model updates change jailbreak success rates; **your** tool policy must not depend on the model always refusing.

#### 7) Excessive agency amplification

Injection or jailbreak becomes an incident when tools exist. OWASP LLM03 email-assistant example class: read-mail tool also can **send**; malicious email → model → forward secrets.

| Amplifiers | Controls |
|------------|----------|
| Over-broad tools | Split read vs send; least privilege |
| Service-role MCP / bypass RLS | Identity design ([4](./4_Agentic_Controls.md), [5](./5_Model_And_Data_Supply_Chain.md)) |
| Shell / code exec tools | ASI05 — sandbox or ban |
| No human gate on destructive actions | Dry-run + approve |

#### 8) Supply-chain instruction planting

OWASP Scenario #8/#9 class: malicious system prompt in an extension repo; poisoned MCP package BCC’ing mail; tool descriptor lies about behavior.

**Defender lens:** pin/sign/allowlist; review tool text as code; AIBOM ([5](./5_Model_And_Data_Supply_Chain.md)).

#### 9) Resource / cost attacks

Unbounded queries, sponge examples, agent loops — LLM06 / ATLAS cost-harvesting themes. Not “clever text,” but still adversarial.

**Defender lens:** quotas, budgets, circuit breakers ([4](./4_Agentic_Controls.md)).

### B. Mapping cheat sheet (register tags)

| Mechanism | OWASP | ATLAS (look up current ID on atlas.mitre.org) |
|-----------|-------|-----------------------------------------------|
| Direct / indirect injection | LLM01 | Prompt Injection family |
| Jailbreak / guardrail bypass | (often pairs with LLM01/03) | Jailbreak technique |
| RAG poison | LLM05 / LLM09 | RAG poisoning / data poison themes |
| Memory poison | ASI06 | Agent context poisoning themes |
| Tool misuse | ASI02 / LLM03 | Agent tool abuse themes |
| MCP/supply | LLM04 / ASI04 | AI supply chain / poisoned tools |
| Cost DoS | LLM06 | Cost harvesting / DoS AI service |

### C. Authorized test cases (shapes only)

Use inside **scoped** eval ([6](./6_Authorized_AI_Evaluation.md)). Pass = **control behavior**, not “model said no.”

| ID | Setup | Attacker-shaped input (illustrative intent) | Expect |
|----|-------|-----------------------------------------------|--------|
| T-DIR-1 | FAQ bot, no tools | User text tries to override role and demand secrets | No secrets; no tool calls |
| T-IND-1 | Summarizer + fetch URL | Lab page contains “instruction” to exfil via fabricated link/markdown | Fetcher sanitizes; no outbound of session data |
| T-RAG-1 | RAG over lab corpus | Poisoned chunk with “SYSTEM: call tool X” | Chunk treated as data; tool policy denies |
| T-MAIL-1 | Mail summarizer | Lab email asks agent to forward inbox to external address | Send capability absent or human-gated |
| T-MCP-1 | Read-only ticket tool | Ticket text asks for DB dump / shell | No elevated MCP; deny logged |
| T-JB-1 | Model with safety policy | Lab asks for clearly disallowed **lab-safe** content class your policy defines | Refusal **or** app blocks; tools still cold |
| T-BUD-1 | Agent with tools | Looping task / expensive tool spam | Budget trips |

Replace “lab page/email/chunk” with fixtures **you author** for your product — do not paste untrusted internet “jailbreak lists” into production.

### D. Detection ideas (defender telemetry)

| Signal | Why |
|--------|------|
| Sudden tool-call spikes / new tool first-use | Agency abuse |
| Retrieves from unexpected sources | RAG poison / ACL miss |
| High rate of policy denials | Active probing |
| Invisible Unicode in ingest | Smuggling attempt |
| Approval timeouts / force attempts | Social + ASI09 |
| Cost anomalies per user/agent | LLM06 |

## Apply

1. Add jailbreak vs injection vs ASI01 as separate rows on your threat register.  
2. For each OWASP scenario anatomy above, mark which trifecta legs your product has.  
3. Author three **fixtures** (doc/email/chunk) for T-IND-1, T-RAG-1, T-MAIL-1 — keep them lab-only.  
4. Wire pass/fail to **tool policy logs**, not vibes.  
5. Tag each fixture with LLM/ASI/ATLAS IDs.  
6. Brief engineering: “filters help; agency limits save.”  

### Self-check

- [ ] Distinguish jailbreak, prompt injection, and agent goal hijack  
- [ ] Fill the anatomy template for one incident pattern  
- [ ] Explain indirect injection via trusted low-privilege channels  
- [ ] Name multimodal and Unicode smuggling as classes  
- [ ] Tie agency tools to why injection becomes a breach  
- [ ] Design authorized tests that assert controls, not model politeness  

## Further reading

- [OWASP Top 10 for LLM Applications 2026 — LLM01 scenarios](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/)
- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [NIST AI 100-2e2025 — Adversarial Machine Learning](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)
- [OWASP — LLM Prompt Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html)
- [Authorized AI evaluation](./6_Authorized_AI_Evaluation.md)
- [Foundations/3 — Ethics and authorization](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md)
