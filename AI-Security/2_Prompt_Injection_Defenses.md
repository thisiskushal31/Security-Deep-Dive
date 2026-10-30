# Prompt injection defenses (architecture)

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to **design systems that survive** prompt injection. How attacks unfold end-to-end (plant → read → steer → act → persist): [8](./8_Attack_Mechanisms_For_Defenders.md). Catalog ownership: LLM01 in [1](./1_LLM_App_Threat_Model_In_Practice.md). RAG surfaces: [3](./3_RAG_And_Memory_Hardening.md). Tools and gates: [4](./4_Agentic_Controls.md).

LLMs do **not** cleanly separate instructions from data. Tokens are tokens. **No reliable prevention** exists today: filters, “ignore previous instructions” prose, and “safe model” marketing all degrade against adaptive attackers. Defense is **architectural** — assume the instruction boundary will be bypassed; constrain what the model can *do* and what outputs can *reach*.

Entry vectors sit on the outside of the attack surface; amplifiers (retrieval, tools, multi-agent) sit in the middle; impacts sit at the core. Place controls so a successful steer still dies before impact:

![Attack surface as bullseye — entry, amplifiers, impacts](../Assets/AI-Security/OWASP_LLM_2026_Attack_Surface_Bullseye.png)

*Credit: OWASP Top 10 for LLM Applications 2026, Figure 2.*

```text
Assume: model will eventually follow attacker-shaped instructions
Therefore:
  credentials & side effects live in trusted code + policy
  model output is untrusted data until validated
  privileged actions need deterministic authZ ± human gate
```

## Concept

### What prompt injection is (for builders)

| Type | Meaning | Typical plant |
|------|---------|---------------|
| **Direct** | Attacker (or careless user) types instructions into the chat channel that compete with the system role | Chat, API `messages[]` |
| **Indirect** | Instructions arrive as *data* the app later retrieves or summarizes — the attacker never opens your UI | Email, web page, ticket, RAG chunk, tool return, memory |
| **Cross-modal** | Instructions hide in image/audio/other modalities and become tokens after OCR, ASR, or vision encoding | Screenshots, PDFs-as-images, voice notes |

Injection is the **input-boundary** failure. It is not the same as sensitive disclosure (LLM02), excessive agency (LLM03), or improper output handling (LLM10) — those are how a successful injection *hurts*. Fix the boundary *and* the blast radius.

### How injection reaches production (defender view)

Walk the chain once before you place filters:

1. **Plant** — instruction-shaped content lands on a surface your system will treat as ordinary data.
2. **Privileged read** — an assistant with useful credentials (mailbox, repo, CRM, DB tool) ingests that content.
3. **Steer** — the model’s next tokens follow the plant more than the developer’s system intent.
4. **Act** — tools fire, or a human approves a fluent plan.
5. **Persist** — optional: the instruction is written into RAG or long-term memory and returns on later retrieves.

Every durable control in this note targets one of those steps. Phrase filters only touch a thin slice of step 3.

### Jailbreak vs injection vs goal hijack

| Attacker’s goal | Label | First controls |
|-----------------|-------|----------------|
| Steer *this application* this turn / via docs/tools | Prompt injection (LLM01) | This note + [3](./3_RAG_And_Memory_Hardening.md) + [4](./4_Agentic_Controls.md) |
| Bypass *model safety refusals* (disallowed topics, dual-use help) | Jailbreak | Still contain tools; refusal is not your only control — [8](./8_Attack_Mechanisms_For_Defenders.md) |
| Redirect multi-step agent objectives and plans | Agent goal hijack (ASI01) | Planner constraints, memory integrity, human gates — [4](./4_Agentic_Controls.md) |

Colloquial speech collapses all three into “jailbreak.” Your threat register and control design must not.

## Advanced

### Defense-in-depth stack (place in order of residual risk)

Apply as layers. Prefer **containment** over “perfect filter.” After each layer, ask: if the model complies with the attacker, does this still stop impact?

| # | Control | Breaks which chain step | Detail |
|---|---------|-------------------------|--------|
| 1 | **Constrain role in system prompt** | Steer (partial) | Declarative allow/deny (“assist with X only”). Partial — attackers who infer the prompt can bypass; pair with #4 |
| 2 | **Strict output schema + validate in app code** | Act | Structural validation in trusted code — not a second LLM. Schema-valid can still carry malicious SQL or an exfil email body |
| 3 | **Filter every modality boundary** | Plant / privileged read | Text, image, audio, structured data; OCR/transcribe then filter extracted text. Semantic filters fail on paraphrase, encoding, low-resource / code-mixed input |
| 4 | **Credentials & state-change in application code** | Act | Least privilege per operation; **deterministic policy engine** re-validates intent and args at execution time |
| 5 | **Strip invisible Unicode** | Plant (partial) | Tag-block U+E0000–E007F; variation selectors U+FE00–FE0F; zero-width U+200B/C/D, U+2060 — at every ingest **and** render boundary. Does not stop visible-text payloads |
| 6 | **Provenance-labeled channels for external content** | Steer | Structurally separate “data” from “instructions.” Helps in non-adaptive tests; adaptive attackers who know the marking scheme can mimic it |
| 7 | **Human approval for high-impact actions** | Act | Show the **exact** rendered action (not a summary) before delete/pay/publish/mail-all; timeout **fail-closed**. Invisible Unicode can desync display vs execute |
| 8 | **Rule of Two / capability budgets** | Amplify | `[A,B,C]` = untrusted input + sensitive data + state-change/external → per-action human approval. Also max tool calls, tokens, spend, recursion — [4](./4_Agentic_Controls.md) |
| 9 | **Privileged memory writes** | Persist | Log causing prompt; classify instruction/role-modification writes; require approval before cross-session persist — [3](./3_RAG_And_Memory_Hardening.md) |
| 10 | **Pin/sign MCP & tools; audit full tool defs** | Plant (supply chain) | Descriptions, schemas, annotations — hash complete definitions; monitor rug pulls — [5](./5_Model_And_Data_Supply_Chain.md) |
| 11 | **Adaptive adversarial evaluation** | Measure | Disclose the defense to testers; reject static-only “near zero ASR” claims — [6](./6_Authorized_AI_Evaluation.md) |

```text
Untrusted content
  → [ingest strip / quarantine / delimit]
    → Orchestrator (system rules + data blocks)
      → Model (may be steered)
        → Schema validate (code)
          → Policy engine on tool args
            → allow | deny | require human
              → Tool invoke (least privilege identity)
```

### How each common mechanism dies (or does not)

| Mechanism ([8](./8_Attack_Mechanisms_For_Defenders.md)) | What happens without architecture | Control that actually cuts it |
|--------------------------------------------------------|-----------------------------------|-------------------------------|
| Direct override in chat | Model plays along; tools fire | No privileged tools from chat alone; policy on args |
| Indirect via web/email/ticket | Low-privilege plant + high-privilege reader | Separate read vs send identities; delimit + quarantine HTML |
| Indirect via RAG chunk | Every future retrieve re-steers | ACL-before-retrieve; ingest provenance; [3](./3_RAG_And_Memory_Hardening.md) |
| Payload split / obfuscation | Deny-list misses fragments | Capability limits; never rely on phrase match |
| Invisible Unicode | Humans miss; model may not | Strip at every boundary **and** agency containment |
| Multimodal stego | Vision/OCR surfaces hidden instructions | Modality pipeline policy; low privilege for vision agents |
| Jailbreak then “call this tool” | Refusal fails; impact is the tool | Deterministic tool authZ; human gate; budgets |
| MCP / tool descriptor poison | Agent trusts lying schema | Pin/sign servers; review descriptors as code |

### Output handling (LLM10) — always on

Model output may become HTML, SQL, shell, email body, webhook payload, or config. Injection often *ends* as improper output handling: the model is steered to emit a payload your sink executes.

| Do | Don’t |
|----|-------|
| Encode/escape for the sink | `eval` model text |
| Parameterized queries / safe APIs | Concatenate model text into shell |
| Allowlist URLs and actions in policy code | “The model said the URL is safe” |
| Treat Markdown/HTML as untrusted until sanitized | Render raw model HTML in admin consoles |

### Separating content that looks like instructions

| Pattern | Practice |
|---------|----------|
| Retrieved / tool content | Explicit delimiters + “treat as data only”; reinforce system rules **after** untrusted blocks (test per model — order is not a guarantee) |
| Email / web connectors | Strip or quarantine HTML; never auto-invoke tools from untrusted mail without gates |
| Multimodal | Never assume “user can’t see it ⇒ model can’t follow it” |
| Memory writes | Facts vs instructions; write ACL — [3](./3_RAG_And_Memory_Hardening.md) |

### What will not save you alone

| Weak control | Why it fails open |
|--------------|-------------------|
| Deny-lists of phrases (“ignore previous”) | Paraphrase, encoding, split payload, other languages |
| “Please don’t jailbreak” system prose | Not a security boundary |
| Trusting the model to self-police tool use | Circular — you are trusting the compromised component |
| Secrets in system prompts | Turns hidden-context exposure into credential theft |
| “We use a safe model” | Jailbreaks and injection still exist; **tools** decide blast radius |
| A second LLM as the only judge | Same class of failure; use deterministic policy for privileged effects |

### Lethal trifecta / Rule of Two (architecture floor)

| Combo | Requirement |
|-------|-------------|
| **`[A,B,C]`** — untrusted input + sensitive data + state-change/external | Per-action human approval before production |
| **`[A,B]` or `[A,C]`** | Explicit residual-risk assessment on the threat register |
| Remove any one leg | High-impact conditions collapse |

Do not ship `[A,B,C]` agents “because the model is safe.” The model is the component you assume will be steered ([1](./1_LLM_App_Threat_Model_In_Practice.md), [8](./8_Attack_Mechanisms_For_Defenders.md)).

## Apply

1. List every path untrusted content enters your app (user, RAG, tools, memory, multimodal).  
2. For each privileged action, write whether authZ is **deterministic code** or “model decided.” Fix the latter.  
3. Define an output schema for one endpoint; list validation rules in trusted code.  
4. Draw the lethal trifecta for your agent; remove or gate one leg.  
5. Spec a human-approval UX for one high-impact tool (target, args, blast radius the human must see).  
6. Add invisible-Unicode stripping to every ingest and render boundary.  
7. Implement authorized fixtures from [8](./8_Attack_Mechanisms_For_Defenders.md) T-DIR-1, T-IND-1, T-RAG-1 — assert **tool denials** in logs, not polite chat refusals.

### Self-check

- [ ] State why prevention is unreliable and architecture is required  
- [ ] Narrate plant → read → steer → act → persist and name a control per step  
- [ ] Distinguish direct, indirect, and cross-modal injection  
- [ ] Distinguish injection vs jailbreak vs ASI01  
- [ ] Recite the containment stack (credentials in code, schema validate, least privilege, human gates)  
- [ ] Explain LLM01 vs LLM03 vs LLM10  
- [ ] Name three “filters only” anti-patterns  

## Next

- [RAG and memory hardening](./3_RAG_And_Memory_Hardening.md)

## Further reading

- [OWASP LLM01 — Prompt Injection (Top 10 for LLM Applications 2026)](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/)
- [OWASP — LLM Prompt Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html)
- [NIST AI 100-2e2025 — Adversarial Machine Learning](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)
- [CISA / NCSC — Guidelines for secure AI system development](https://www.cisa.gov/resources-tools/resources/guidelines-secure-ai-system-development)
- [Attack mechanisms for defenders](./8_Attack_Mechanisms_For_Defenders.md)
