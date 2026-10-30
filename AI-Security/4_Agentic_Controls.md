# Agentic controls (tools, identity, gates, kill switch)

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

Controls for systems that **plan and act** — tools, non-human identity, human oversight, budgets, and emergency stop. Catalog: LLM03 / ASI02–03 / ASI05 / ASI08–10. Injection: [2](./2_Prompt_Injection_Defenses.md). Supply chain / MCP: [5](./5_Model_And_Data_Supply_Chain.md). How agency turns text into an incident: [8](./8_Attack_Mechanisms_For_Defenders.md).

## Concept

### Agency without babysitting the model

The model **proposes**; trusted runtime **disposes**. Hold credentials and state-change in application code. Re-validate every privileged effect with a **deterministic policy engine** at execution time. If the model is steered, the policy — not the model’s confidence — decides whether anything happens.

```text
Plan (model) → Policy check (code) → Optional human gate → Tool invoke → Log / budget
                      │
                      └── deny / require approval / allow (narrow args)
```

Start agentic automation on **low-risk** tasks. Keep humans on high-impact decisions. AI-specific controls **add to** patching, identity, and logging — they do not replace them.

**Rule of Two floor:** `[A]` untrusted input, `[B]` sensitive data, `[C]` state-change or external communication. Any `[A,B,C]` agent needs **per-action** human approval. `[A,B]` or `[A,C]` needs documented residual-risk acceptance.

### Control pillars

| Pillar | Intent | Failure if missing |
|--------|--------|--------------------|
| **Least privilege tools** | Per-tool scopes; no “do anything” API token | One steer → many systems |
| **Identity** | Distinct non-human identities; no silent inheritance of user god-rights (ASI03) | Confused deputy; stolen user OAuth everywhere |
| **Human-in-the-loop** | High-impact actions need explicit approval + preview (ASI09 hygiene) | Automation bias on fluent plans |
| **Budgets & sandboxes** | Tokens, $, time, recursion, egress caps (LLM06 / ASI08) | Cost bombs; cascade |
| **Kill switch & degradation** | Out-of-band stop; intermediate modes (read-only, tools off) | No containment mid-incident |

### How tool-enabled compromise actually happens

Most severe public patterns are **injection + agency**, not “the model said something rude”:

1. Content is planted where a privileged agent will read it (mail, ticket, web, RAG).
2. The agent’s plan includes a tool call the plant requested (forward mail, query DB, run shell, call webhook).
3. If there is no policy engine, the runtime treats model JSON as authorization.
4. If there is a rubber-stamp “Approve” button showing only fluent prose, a human becomes the gadget (ASI09).
5. If one identity holds read+send+admin, blast radius spans the trifecta.

```text
Steered model
  → proposes tool calls
    → IF policy missing: tools run as user/service
    → IF policy present: deny / require human / allow narrow args
         │
         └── kill switch / budgets cut amplification
```

## Advanced

### Tool misuse prevention (ASI02)

| Control | Practice |
|---------|----------|
| Explicit authn per tool invocation | Not “session said yes once” forever |
| Allowlist tools per agent role | Customer-bot ≠ refund-bot ≠ shell-bot |
| Argument validation in code | Schema + business rules (amount caps, destination allowlists) |
| Dry-run / diff for destructive actions | Show plan before delete / transfer / publish |
| Treat tool descriptions as untrusted | MCP annotations can lie — pin/sign servers ([5](./5_Model_And_Data_Supply_Chain.md)) |
| Rate / count limits | Over-invoke and cost bombs |

**Misuse vs privilege abuse:** ASI02 = unsafe use of *granted* tools; ASI03 = wrong identity or privilege. Fix both — a perfect policy on a god-token still fails.

### Identity and privilege (ASI03)

| Do | Don’t |
|----|-------|
| Agent service accounts with narrow IAM roles | Embed long-lived admin keys in the prompt or agent env |
| Short-lived tokens; audience-bound | Pass-through user OAuth to every tool by default |
| Separate “read corp wiki” from “send email” principals | One bot identity for all side effects |
| Separate “read mailbox” from “send mailbox” | Summarize-mail tool that can also send |

**Trusted-surface trap:** the plant channel looks safe (GitHub issue, CRM ticket). Blast comes from **who reads it** and **which principal** they hold.

### Human oversight

| Requirement | Detail |
|-------------|--------|
| Risk-tiered autonomy | Auto-allow low; approve medium/high |
| Action preview | Human sees the **exact** rendered action (target, full args, data classes, blast radius) — not a summary that can hide smuggled differences |
| Interrupt / rollback | User can stop mid-plan; queued tool calls cancel |
| Approval timeout fail-closed | No approval ⇒ **block**, do not proceed |
| Audit trail | Who approved what, when, with which preview hash |

**Trust exploitation (ASI09):** fluent confidence ≠ correctness. Approval UIs must surface risk, citations, and uncertainty — not only polished prose. If the UI only shows “Agent recommends: send,” you built a social-engineering sink. Invisible Unicode can make the displayed action differ from the executed one — strip at render boundaries ([2](./2_Prompt_Injection_Defenses.md), [8](./8_Attack_Mechanisms_For_Defenders.md)).

### Unexpected code execution (ASI05)

| Ban / gate | Why |
|------------|-----|
| `eval` / shell from model text | Text → RCE |
| Unsafe deserialization from tool I/O | Classic exploit path |
| Unsandboxed code interpreters | Escape → host compromise |

Prefer sealed sandboxes with no secret mount, strict egress, ephemeral filesystem, and no privilege to the orchestrator host.

### Budgets and cascading failure (LLM06 / ASI08)

| Budget | Example | Failure prevented |
|--------|---------|-------------------|
| Max tool calls / turn or /task | Hard stop after N | Agent loops |
| Max recursion / sub-agent depth | Cap swarm fan-out | Cascading A2A |
| Token and $ caps | Per user / per agent / per day | Unbounded consumption |
| Wall-clock timeout | Kill hung plans | Stuck workers |
| Circuit breaker | Trip on error rate / anomaly | Runaway after partial outage |

### Kill switch and intermediate states

| Capability | Requirement |
|------------|-------------|
| **Manual kill switch** | Halt inference, streaming, **queued tool calls**, MCP sessions, workers — not only the chat UI |
| **Out-of-band channel** | Infrastructure / control plane — **not** mediated by the agent |
| **Exercise regularly** | Documented drills with evidence (time-to-halt metric) |
| **Intermediate modes** | Read-only; disable specific MCP; drop a retrieval source; smaller/safer model — without full outage |

```text
Full power → (trip) → Degraded (tools limited / read-only) → Kill (all stop)
                         ↑ human / SOC / platform control plane
```

### Inter-agent and rogue-agent hygiene (ASI07 / ASI10)

| Risk | Control |
|------|---------|
| Unauthenticated agent-to-agent messages | Authn + integrity on A2A; no “trust the other bot” |
| Goal drift / rogue behavior | Drift monitors; version pins; kill switch; change control on agent graphs |
| Shared writable memory across agents | Scope memory; write ACL ([3](./3_RAG_And_Memory_Hardening.md)) |

### Logging for agents

Log: plan summary, tool name, args (redact secrets), policy decision, approver, budgets remaining, model/version, MCP server id. Feed SOC — Defensive-Ops owns SIEM depth. Eval pass/fail depends on these fields ([6](./6_Authorized_AI_Evaluation.md)).

## Apply

1. Inventory every tool; assign risk tier and identity.  
2. For the highest-risk tool, write policy rules in pseudocode (allow / deny / approve).  
3. Design approval UX fields for that tool (must show blast radius).  
4. Define budgets (calls, $, time, depth) for one agent.  
5. Spec kill-switch scope (what must stop) and an intermediate “read-only” mode.  
6. Schedule a quarterly kill-switch drill; record time-to-halt.  
7. Map three ASI IDs to tickets on your threat register ([1](./1_LLM_App_Threat_Model_In_Practice.md)).  
8. Split any identity that both reads untrusted content and performs external actions.

### Self-check

- [ ] Explain model-proposes / code-disposes  
- [ ] Narrate injection + agency → tool impact without blaming “the model” alone  
- [ ] Distinguish ASI02 vs ASI03  
- [ ] List human-gate requirements (preview, timeout fail-closed)  
- [ ] Define kill switch as more than stopping chat tokens  
- [ ] Name budget classes that limit cascade (ASI08)  
- [ ] State why tool descriptions need supply-chain care  

## Next

- [Model and data supply chain](./5_Model_And_Data_Supply_Chain.md)

## Further reading

- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [OWASP — AI Agent Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/AI_Agent_Security_Cheat_Sheet.html)
- [OWASP — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)
- [CCCS / Five Eyes — Careful adoption of agentic AI](https://www.cyber.gc.ca/en/guidance/careful-adoption-agentic-ai)
- [CSA — MAESTRO](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro)
