# Prompt injection defenses (architecture)

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to **design systems that survive** prompt injection. How attacks work (mechanisms, OWASP scenario anatomies, jailbreak vs injection): [8](./8_Attack_Mechanisms_For_Defenders.md). Catalog: LLM01 in [1](./1_LLM_App_Threat_Model_In_Practice.md). RAG: [3](./3_RAG_And_Memory_Hardening.md). Agency: [4](./4_Agentic_Controls.md).

Official stance (OWASP LLM01:2026, aligned with NIST AML / NCSC messaging): LLMs do **not** cleanly separate instructions from data. **No reliable prevention** exists today. Defense is **architectural**: assume the instruction boundary will be bypassed; constrain what the model can do and what outputs can reach.

## Concept

### What prompt injection is (for builders)

| Type | Meaning |
|------|---------|
| **Direct** | User (or attacker in the chat) supplies instructions that override intended behavior |
| **Indirect** | Instructions arrive via **retrieved docs**, emails, web pages, tool output, images/audio, or memory — not the user’s typed line |
| **Cross-modal** | Instructions hidden in image/audio/other modalities that become tokens after OCR/transcription (OWASP 2026 expansion) |

Prompt injection ≠ sensitive disclosure (LLM02) ≠ excessive agency (LLM03) ≠ improper output handling (LLM10). Injection is the **input-boundary** failure; the others are what make it hurt.

### Design assumption (non-negotiable)

```text
Assume: model will eventually follow attacker-shaped instructions
Therefore: credentials & side effects live in trusted code + policy
          model output is untrusted data until validated
          privileged actions need deterministic authZ ± human gate
```

Filters and “ignore previous instructions” prompts are **partial** controls that degrade against adaptive attackers. Blast-radius controls are what remain.

### Jailbreak vs injection (quick)

| If the attacker’s goal is… | You are usually in… | First controls |
|----------------------------|---------------------|----------------|
| Steer *your app* this turn / via docs/tools | **Prompt injection** (LLM01) | This note + [3](./3_RAG_And_Memory_Hardening.md)+[4](./4_Agentic_Controls.md) |
| Bypass *model safety refusals* | **Jailbreak** (ATLAS jailbreak family) | Still contain tools; don’t rely on refusal alone — [8](./8_Attack_Mechanisms_For_Defenders.md) |
| Redirect multi-step agent objectives | **ASI01 Goal Hijack** | [4](./4_Agentic_Controls.md) |

## Advanced

### Defense-in-depth stack (OWASP LLM01:2026-aligned)

Apply as layers. Prefer **containment** over “perfect filter.”

| # | Control | Detail |
|---|---------|--------|
| 1 | **Constrain role in system prompt** | Declarative allow/deny (“assist with X only”). Partial — pair with privilege controls |
| 2 | **Strict output schema + validate in app code** | Deterministic validation (not a second LLM). Schema-valid can still be malicious semantically — still required |
| 3 | **Filter every modality boundary** | Text, image, audio, structured data; OCR/transcribe then filter extracted text |
| 4 | **Credentials & state-change in application code** | Least privilege per operation; **deterministic policy engine** re-validates intent/args at execution (NIST AI 100-2 / CISA framing) |
| 5 | **Strip invisible Unicode / steganographic classes** | Tag-block, variation selectors, zero-width at ingest/render — does not stop visible-text payloads |
| 6 | **Provenance-labeled channels for external content** | Structurally separate “data” from “instructions” where tooling allows |
| 7 | **Human approval for high-impact actions** | Dry-run / diff preview before delete, pay, publish, mail-all |
| 8 | **Capability budgets** | Max tool calls, tokens, spend, recursion — see [4](./4_Agentic_Controls.md) |
| 9 | **Adversarial evaluation** | Treat model as untrusted user; test trust boundaries ([6](./6_Authorized_AI_Evaluation.md)) — process, not payload cookbook here |
| 10 | **Pin/sign MCP & tools; audit tool descriptions** | Tool text can carry instructions — supply chain ([5](./5_Model_And_Data_Supply_Chain.md)) |

### Output handling (LLM10) — always on

Model output may become HTML, SQL, shell, email body, or config. Rules:

| Do | Don’t |
|----|-------|
| Encode/escape for the sink | `eval` model text |
| Parameterized queries / safe APIs | Concatenate model text into shell |
| Allowlist URLs/actions in policy code | “The model said the URL is safe” |

### Separating content that looks like instructions

| Pattern | Practice |
|---------|----------|
| Retrieved / tool content | Delimiters + “treat as data only”; reinforce system rules **after** untrusted blocks ([3](./3_RAG_And_Memory_Hardening.md)) |
| Email/web connectors | Strip or quarantine HTML; no auto-tooling from untrusted mail without gates |
| Multimodal | Never assume “user can’t see it ⇒ model can’t follow it” |

### What will not save you alone

| Weak control | Why |
|--------------|-----|
| Deny-lists of phrases (“ignore previous”) | Trivial paraphrase / encoding / split-payload bypass ([8](./8_Attack_Mechanisms_For_Defenders.md)) |
| “Please don’t jailbreak” system prose | Not a security boundary |
| Trusting the model to self-police tool use | Circular — the compromised component |
| Secrets in system prompts | Turns LLM08 into credential theft |
| “We use a safe model” | Jailbreaks and injection still exist; tools decide blast radius |

### Control mapping from common mechanisms

| Mechanism ([8](./8_Attack_Mechanisms_For_Defenders.md)) | Primary defenses here |
|--------------------------------------------------------|------------------------|
| Direct override | Role constraint + **no** privileged tools from chat alone |
| Indirect (web/email/RAG/MCP text) | Provenance channel; delimiters; ACL; policy engine on tools |
| Split / obfuscated / invisible Unicode | Strip + capability limits (filters partial) |
| Multimodal stego | Modality filters; low privilege for vision agents |
| Jailbreak then “call tool” | Deterministic tool authZ; human gate; budgets |

## Apply

1. List every path untrusted content enters your app (user, RAG, tools, memory, multimodal).  
2. For each privileged action, write whether authZ is **deterministic code** or “model decided.” Fix the latter.  
3. Define an output schema for one endpoint; list validation rules in trusted code.  
4. Draw the lethal trifecta for your agent; remove or gate one leg.  
5. Spec a human-approval UX for one high-impact tool (what the human must see).  
6. Add invisible-Unicode stripping to the ingest checklist.  
7. Implement authorized fixtures from [8](./8_Attack_Mechanisms_For_Defenders.md) T-DIR-1, T-IND-1, T-RAG-1 — assert **tool denials** in logs.

### Self-check

- [ ] State why prevention is unreliable and architecture is required  
- [ ] Distinguish direct, indirect, and cross-modal injection  
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
