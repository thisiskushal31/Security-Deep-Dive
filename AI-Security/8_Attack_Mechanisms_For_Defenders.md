# Attack mechanisms for defenders

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How AI-application attacks unfold end-to-end — so you can place controls where the chain breaks. Pair with architecture defenses in [2](./2_Prompt_Injection_Defenses.md)–[5](./5_Model_And_Data_Supply_Chain.md) and scoped testing in [6](./6_Authorized_AI_Evaluation.md). Technique catalog: [MITRE ATLAS](https://atlas.mitre.org/) (`AML.T0051` prompt injection, `AML.T0054` jailbreak).

Work only in environments you own or are authorized to test ([Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md)).

## Concept

An LLM does not maintain a hard wall between “instructions” and “data.” Both arrive as tokens on one stream. Anything that can influence those tokens — a chat box, a PDF in RAG, an email the agent summarizes, an image encoder, a tool return value, a poisoned MCP descriptor, long-term memory — can steer behavior. Damage appears when that steered behavior reaches **tools, data, or humans who trust fluent output**.

Three deployment properties make this worse:

| Property | Meaning for defenders |
|----------|------------------------|
| **Context-window pooling** | System prompt, user text, retrieved docs, tool outputs, history, and memory share one stream — no enforced trust boundary |
| **Memory / corpus persistence** | One poisoned write taints every later session that retrieves it |
| **Agentic execution** | Model output drives tools; tool outputs re-enter context → chained or self-replicating effects |

Decompose every scenario on three axes before you pick controls:

| Axis | Questions |
|------|-----------|
| **Delivery surface** | Direct chat? Retrieved web/RAG/email? Tool output? MCP channel? Memory? |
| **Propagation** | Single-shot? Multi-step kill chain? Cross-session via RAG/memory? Self-replicating across agents? |
| **Encoding** | Plain text? Split fields? Base64/ROT13/emoji? Invisible Unicode? Multimodal stego? Low-resource language? |

Entry vectors sit on the outside of the attack surface; amplifiers sit in the middle; impacts sit at the core:

![OWASP LLM Top 10 2026 attack-surface bullseye (Figure 2)](../Assets/AI-Security/OWASP_LLM_2026_Attack_Surface_Bullseye.png)

*Credit: OWASP Top 10 for LLM Applications 2026, Figure 2 (attack surface as bullseye).*

```text
Perimeter entry (chat, RAG, email, web, image, MCP text, memory)
        │
        ▼
Amplifiers (retrieval, planning, tools, A2A, privileges)
        │
        ▼
Core impact (exfil, fraud, integrity loss, host compromise, cost burn)
```

### Three labels that must not be collapsed

| Label | ATLAS / catalog | What the adversary is doing | Where you fix it first |
|-------|-----------------|----------------------------|-------------------------|
| **Prompt injection** | `AML.T0051` (+ `.000` Direct / `.001` Indirect / `.002` Triggered); LLM01 | Steer *this application’s* behavior via attacker-shaped content | App orchestration, connectors, tool policy |
| **Jailbreak** | `AML.T0054` | Push the **model** past safety/alignment refusals | Still contain tools; never assume refusal is your only control |
| **Agent goal hijack** | ASI01 | Redirect multi-step objectives and plans | Planner constraints, human gates, memory integrity |

Jailbreak is often discussed as a *subset* of injection when the goal is to violate safety protocols. Your threat register should still track them separately: different first controls, different eval fixtures.

### How a typical high-impact chain actually happens

1. **Plant** instruction-shaped content somewhere the victim system will later treat as ordinary data (public ticket, web page, email, uploaded doc, package README, image, MCP tool text).
2. **Trigger** a privileged assistant or agent to *read* that content while holding useful credentials (user OAuth, service role, mailbox scope, repo token).
3. **Steer** the model so its next actions follow the planted instructions rather than the developer’s system intent.
4. **Act** through tools (send mail, query DB, run shell, call cloud APIs) or through a human who approves a fluent-looking plan.
5. **Persist** if the instruction was written into RAG or long-term memory — so the next session inherits the compromise.

Break any step and the incident dies: no plant path, no privileged read, no tool, no silent approval, no durable write.

**Lethal trifecta / Rule of Two floor:** treat simultaneous **(A)** untrusted input, **(B)** sensitive data, and **(C)** state change or external communication as high risk. Any `[A,B,C]` agent needs **per-action** human approval. `[A,B]` or `[A,C]` needs an explicit residual-risk assessment. Removing any one leg removes the high-impact conditions.

## Advanced

### A. Direct injection — what “override” looks like in practice

**Catalog scenario shape (direct):** an attacker prompts a support chatbot to ignore guidelines, query private stores, and send mail → unauthorized access and privilege escalation when tools exist.

**Anatomy:** (a) direct user input, (b) single-shot, (c) plain text.

The adversary (or a careless user) types into the same channel as legitimate tasks. The content argues with the system role: claim a new authority, demand secrets, insist tools run “for debugging,” or ask the model to ignore prior constraints.

**Why filters fail:** phrasing varies endlessly; encoding and multi-turn priming change surface form; the model’s helpfulness objective competes with your policy text. Adaptive attackers who have read your defense routinely beat static filters (published adaptive success often far exceeds static “near zero” claims).

**What actually protects you:** the model may produce compliant-looking *text*; your **runtime** must still refuse privileged side effects unless a deterministic policy (and maybe a human) allows them.

```text
User message (attacker-shaped)
  → Orchestrator builds prompt (system + history + user)
    → Model output (may look “jailbroken”)
      → Policy engine on tool args  → allow / deny / approve
           │
           └── never: “model said so” ⇒ execute
```

**Authorized lab check:** against a FAQ bot with a dummy `send_email` tool, attempt role-override language. Pass = **zero** tool invocations in logs even if the chat text plays along.

### B. Indirect injection — the privileged reader problem

Indirect injection is how you lose without the attacker ever opening your chat UI. Content is planted on a **low-privilege write surface** that a **high-privilege reader** (the agent) will ingest. The agent, not the attacker, performs the privileged action.

Trust profile of the plant surface matters:

| Surface trust | Examples | Defender habit |
|---------------|----------|----------------|
| **Untrusted** | Public web, unknown email, search results | Always suspicious |
| **Semi-trusted** | Public issue titles, package READMEs, third-party API text | User chose the platform, not every contributor |
| **“Trusted” trap** | Own repos, CRM tickets, internal mail after an upstream plant | Assume an attacker already wrote here via a form/PR |

#### Published scenario anatomies (defender education)

| # | Pattern | What happens | Anatomy (surface / propagation / encoding) |
|---|---------|--------------|-----------------------------------------------|
| 1 | Direct override | Chatbot steered to query private data / send mail | Direct / single-shot / plain |
| 2 | Web summarize | Hidden page instructions → markdown image URL exfiltrates conversation; user sees only the image | Retrieved web / single-shot + URL exfil / hidden in page source |
| 3 | Unintentional | Job PDF embeds “AI detector” instructions; applicant’s LLM follows them; neither party “attacking” | Document / single-shot / plain |
| 4 | RAG poison | Few poisoned docs in a huge corpus can dominate retrieval (~90% ASR reported with ~5 docs vs millions) | RAG / cross-session / plain |
| 5 | Payload split | Malicious intent split across resume fields; per-field classifiers miss; model recombines | Split fields / recombined at eval / fragmented plain |
| 6 | Multimodal stego | Sub-perceptual image/audio payload extracted by encoder → harmful output or tool call | Image/audio / single-shot / steganographic |
| 7 | Zero-click mail/doc | Crafted email triggers productivity assistant exfil with **no user click**; classifiers bypassed | Email/doc / tool invoke / plain + invisible Unicode channel |
| 8 | Destructive agency | Compromised system prompt in an extension repo, or runtime injection → shell/code exec when tools exist | Supply-chain or runtime / persistent or single-shot / plain |
| 9 | Trusted-backend MCP | Plant in public issue / ticket / npm package; developer agent + elevated MCP dumps repo/DB or BCC’s mail | Trusted-surface MCP / multi-step tool chain / plain |

```text
Attacker  --low privilege-->  Plant store (issue, mail, web, PDF, npm)
                                    │
User/agent later retrieves ─────────┘
                                    │
                         Model + user privileges
                                    │
                         Tool call / exfil / change
```

**Controls that cut the chain:** strip or quarantine HTML; delimit retrieved content as data; authorize retrieval by ACL; split tool identities (read-mail ≠ send-mail); human gate on send/delete; never run developer agents with production `service_role`; pin/sign MCP ([5](./5_Model_And_Data_Supply_Chain.md)).

### C. Encoding, splitting, and invisible channels

Attackers avoid naive deny-lists by changing *representation*, not intent:

| Technique class | Idea | Exact defender response |
|-----------------|------|-------------------------|
| **Payload splitting** | Harmless fragments combine only after retrieve/multi-field/multi-turn | Capability limits; do not rely on per-field phrase match |
| **Invisible Unicode** | Tag-block / zero-width / variation selectors smuggle bytes humans do not see | Strip at **every** ingest and render boundary (see ranges below) |
| **Obfuscation / encoding** | Homoglyphs, Base64, ROT13, emoji, unusual languages, code-mixed text | Partial filters; treat decoded content as untrusted too |
| **Multimodal stego** | Instruction in image/audio below casual notice; encoder surfaces it | Modality pipeline: OCR/ASR → text policy; low privilege for vision agents |
| **Fine-tune / loss oracle** | Attacker uses fine-tune API loss signals to optimize payloads against closed models | Gate fine-tune APIs; monitor anomalous train jobs ([5](./5_Model_And_Data_Supply_Chain.md)) |

**Unicode classes to strip (necessary, incomplete):**

| Class | Code points |
|-------|-------------|
| Tag-block | U+E0000–U+E007F |
| Variation selectors | U+FE00–U+FE0F |
| Zero-width | U+200B, U+200C, U+200D, U+2060 |

Variation-selector variants can smuggle arbitrary bytes invisibly. Stripping does **not** stop visible-language injections. Also: invisible characters can make a **human approval UI** show a different action than the one executed — always show the **exact** rendered action, not a summary.

### D. Jailbreak mechanisms (guardrail bypass) — `AML.T0054`

A jailbreak targets the model’s refusal behavior (disallowed topics, dual-use help, policy blocks). Once refusals fail, the same app still needs tool containment — otherwise “model said yes” becomes “tool ran.”

**Mechanism families** (recognize in authorized eval; build your own fixtures — do not treat this as a third-party attack kit):

| Family | What changes in the dialogue | Why your app still cares |
|--------|------------------------------|--------------------------|
| **Instruction override** | Phrasing that tries to supersede prior constraints | If tools trust model text, bypass ⇒ action |
| **Role / persona switching** | Model asked to adopt a mode that “should” drop refusals | Same |
| **Fictionalization / hypotheticals** | Disallowed content framed as story, screenplay, “educational” | Safety text loses; tools must not |
| **Separate intent from content** | Ask for templates, edge cases, analysis that smuggle the ask | Keyword bans fail |
| **Multi-turn escalation (crescendo)** | Starts benign; gradually crosses policy | History and memory are surfaces |
| **Constrained output formats** | Force JSON/YAML/code/table that slips past refusal heuristics | Schema validate in **code**; still check semantics for tools |
| **Obfuscation / transformation** | Encoding, translation, euphemism | Decode then re-apply policy |
| **High-priority framing** | “Required to complete the evaluation / follow the spec” | Competing objectives |
| **Affirmation priming** | Dialogue shaped so the model continues a complying prefix | Multi-turn logging + tool policy |
| **Algorithmic / optimized strings** | Automated search (black-box or white-box) for jailbreak strings; nonsensical token sequences | Assume adaptive attackers; test with defense disclosed |
| **Weight / adapter / “uncensored” variants** | Fine-tune, LoRA, editing, or removing guardrails on open weights | Supply-chain + allowlisted model hashes ([5](./5_Model_And_Data_Supply_Chain.md)) |

Track jailbreak separately from injection in ATLAS terms, but implement **one** containment story: privileged effects require code + policy ± humans.

### E. Agency amplification — when text becomes an incident

Injection or jailbreak without tools is often a content problem. With tools it becomes a security incident. Excessive agency (LLM03) is what gives LLM01 consequences outside the chat window.

Concrete failure shapes:

| Setup mistake | What goes wrong | Catalog |
|---------------|-----------------|---------|
| Summarize-mail tool includes **send** | Indirect mail injection → forward secrets | LLM03 / ASI02 |
| Single bot identity with broad cloud role | One steered plan touches many systems | ASI03 |
| Shell / code-exec tool for “convenience” | Text → unexpected code execution | ASI05 |
| MCP server with `service_role` DB | Ticket text → data dump (row-level security bypassed) | ASI02 / ASI04 |
| Human clicks “Approve” on fluent plan only | Automation bias | ASI09 |
| Goal rewritten mid-plan by retrieved content | Multi-step behavior follows attacker objective | ASI01 |

```text
Steered model
  → proposes tool calls
    → IF policy missing: tools run as user/service
    → IF policy present: deny / require human / allow narrow args
```

### F. Persistence — RAG and memory

A one-shot chat injection ends when the session ends. A poisoned chunk or memory write returns on every retrieve.

| Persist path | How it happens | Break it |
|--------------|----------------|----------|
| RAG corpus | Malicious or instruction-laden doc passes ingest | Provenance, review, hash baseline, ACL-before-retrieve ([3](./3_RAG_And_Memory_Hardening.md)) |
| Long-term memory | Model/tool writes attacker instructions as “facts”; delayed tool invocation can poison later | Treat **memory writes as privileged**: log causing prompt; classify instruction-like writes; approve before cross-session persist |
| Shared agent memory | One poisoned session contaminates others | Tenant/session isolation |

Factual entries shade into instructions; incremental writes can evade naive per-write classifiers — defense-in-depth still required.

### G. Supply-chain and MCP planting

Not every attack starts in the chat box:

| Plant | Effect | Defender name |
|-------|--------|---------------|
| Malicious system prompt in an extension/repo | Every user of that build inherits bad instructions | Prompt malware |
| Poisoned MCP/tool **description** (invisible to casual UI) | Model follows hidden instructions (read secrets, conceal actions) | **Tool poisoning** |
| Tool definitions change after approval | Trusted tool becomes malicious | **Rug pull** |
| Malicious server’s description steers use of *other* servers’ tools | Cross-server escalation | **Tool shadowing** |
| MCP proxy with static client ID + consent cookie + dynamic registration | Attacker obtains tokens without real consent | **Confused deputy** |
| Tampered model / LoRA / merge / namespace reuse | Behavioral backdoor before your app logic runs | Model supply chain (LLM04) |
| Malicious npm MCP package (e.g. BCC-on-send pattern) | Side-channel exfil across orgs | Package allowlist + pin |

Pin, sign, allowlist, hash the **complete** tool definition (including `inputSchema` / annotations — a flipped `destructiveHint` changes client behavior without a visible description change). Review tool descriptors as code ([5](./5_Model_And_Data_Supply_Chain.md)).

### H. Cost and availability abuse

Flooding inference, sponge / energy-latency patterns, agentic tool fan-out, “summarize 1000 times,” recursive self-delegation, or growing context in long agent sessions burn money and capacity (LLM06 / ATLAS resource-consumption techniques). Budgets and circuit breakers are the controls ([4](./4_Agentic_Controls.md)).

### I. Defense placement map

| Chain step | Break with |
|------------|------------|
| Plant | Ingest controls, connector hygiene, supply-chain gates, Unicode strip |
| Privileged read | Least-privilege identities; separate read/send |
| Steer | Delimiters, provenance channels, assume bypass (adaptive attackers) |
| Act | Deterministic policy, human gates showing **exact** action, no shell-by-default |
| Persist | ACL-before-retrieve, privileged memory writes |
| Amplify | Rule of Two / budgets, kill switch, no `[A,B,C]` in prod without per-action approval |

### J. Authorized fixtures (control assertions)

| ID | Intent under test | Pass signal |
|----|-------------------|-------------|
| T-DIR-1 | Direct override against FAQ bot | No tool calls |
| T-IND-1 | Lab HTML with instruction-like text + image-URL style sink | No session exfil; sanitizer/policy holds |
| T-RAG-1 | Poisoned chunk in lab index | Retrieve only if ACL ok; tools cold |
| T-SPLIT-1 | Split instruction across two lab fields | Classifier alone may fail; tools still cold |
| T-MAIL-1 | Lab email asking to forward inbox | Send absent or human-gated + denied without approval |
| T-MCP-1 | Ticket text demanding DB/shell | Elevated MCP absent; deny logged |
| T-UNI-1 | Lab string with zero-width / tag-block / VS chars | Stripped at ingest; approval UI matches executed args |
| T-JB-1 | Lab-defined disallowed class (instruction-override + crescendo shapes) | Refusal **or** app block; tools cold |
| T-MEM-1 | Attempt to write instruction-like memory | Write blocked or human-approved |
| T-BUD-1 | Looping expensive tools | Budget trips |

Author fixtures yourself for your product. Pass/fail on **telemetry**, not vibes. Red-team with the **defense specification disclosed** to testers — static-only “attack success near zero” claims are not evidence.

## Apply

1. Pick one production-like design; mark plant → read → steer → act → persist on a whiteboard; label delivery / propagation / encoding.  
2. For each step, name the control that fails open today.  
3. Open tickets until `[A,B,C]` is broken for every agent (or per-action approval + residual-risk acceptance exists).  
4. Build T-IND-1, T-RAG-1, T-UNI-1, T-MCP-1 fixtures; wire CI or staging checks to tool-deny logs.  
5. Add separate register rows for injection (`AML.T0051`), jailbreak (`AML.T0054`), and ASI01.  
6. Drill the kill switch while a staged “steered” tool queue is active ([4](./4_Agentic_Controls.md)).  
7. Hash and review every MCP tool definition in prod; alert on schema drift (rug-pull detection).

### Self-check

- [ ] Narrate the five-step high-impact chain and the three anatomy axes  
- [ ] Explain indirect injection via a low-privilege plant + high-privilege reader  
- [ ] Recite Unicode strip ranges and why approval UIs must show exact actions  
- [ ] List jailbreak mechanism families and why tools still need containment  
- [ ] Name tool poisoning, rug pull, shadowing, confused deputy  
- [ ] Place Rule of Two `[A,B,C]` on your agents  
- [ ] Define pass/fail for three authorized fixtures using logs  

## Further reading

- [OWASP Top 10 for LLM Applications 2026](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/)
- [OWASP LLM01 Prompt Injection (2026 final text)](https://github.com/GenAI-Security-Project/GenAI-LLM-Top10/blob/main/2026/final/LLM01_PromptInjection.md)
- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [NIST AI 100-2e2025 — Adversarial Machine Learning](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)
- [Model Context Protocol — Security best practices](https://modelcontextprotocol.io/docs/2025-06-18/tutorials/security/security_best_practices)
- [OWASP — MCP Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/MCP_Security_Cheat_Sheet.html)
- [OWASP — LLM Prompt Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LLM_Prompt_Injection_Prevention_Cheat_Sheet.html)
- [Authorized AI evaluation](./6_Authorized_AI_Evaluation.md)
