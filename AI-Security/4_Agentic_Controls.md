# Agentic controls (tools, identity, gates, kill switch)

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

Controls for systems that **plan and act** — tools, non-human identity, human oversight, budgets, and emergency stop. Catalog: LLM03 / ASI02–03 / ASI05 / ASI08–10. Injection: [2](./2_Prompt_Injection_Defenses.md). Supply chain/MCP: [5](./5_Model_And_Data_Supply_Chain.md). Cheat sheet: [OWASP AI Agent Security](https://cheatsheetseries.owasp.org/cheatsheets/AI_Agent_Security_Cheat_Sheet.html).

## Concept

### Agency without babysitting the model

The model proposes; **trusted runtime** disposes. OWASP: hold credentials and state-change in application code; re-validate with a **deterministic policy engine** at execution time.

```text
Plan (model) → Policy check (code) → Optional human gate → Tool invoke → Log / budget
                      │
                      └── deny / require approval / allow
```

### Control pillars

| Pillar | Intent |
|--------|--------|
| **Least privilege tools** | Per-tool scopes; no “do anything” API token |
| **Identity** | Distinct non-human identities; no silent inheritance of user god-rights (ASI03) |
| **Human-in-the-loop** | High-impact actions need explicit approval + preview (ASI09 hygiene) |
| **Budgets & sandboxes** | Tokens, $$, time, recursion, egress caps (LLM06 / ASI08) |
| **Kill switch & degradation** | Out-of-band stop; intermediate modes (read-only, tools off) |

CCCS / Five Eyes: start agentic automation on **low-risk** tasks; keep humans on high-impact decisions.

## Advanced

### Why injection becomes a breach here

Most severe public patterns (OWASP LLM01 scenarios #7–#9 class — email agents, MCP, shell tools) are **injection + agency**. Mechanisms: [8](./8_Attack_Mechanisms_For_Defenders.md). This note is the blast-radius layer those scenarios demand.

### Tool misuse prevention (ASI02)

| Control | Practice |
|---------|----------|
| Explicit authn per tool invocation | Not “session said yes once” forever |
| Allowlist tools per agent role | Customer-bot ≠ refund-bot ≠ shell-bot |
| Argument validation in code | Schema + business rules (amount caps, dest allowlists) |
| Dry-run / diff for destructive actions | Show plan before delete/transfer/publish |
| Treat tool descriptions as untrusted | MCP annotations can lie — pin/sign servers ([5](./5_Model_And_Data_Supply_Chain.md)) |
| Rate / count limits | Over-invoke and cost bombs |

**Misuse vs privilege abuse:** ASI02 = unsafe use of *granted* tools; ASI03 = wrong identity/privilege. Fix both.

### Identity and privilege (ASI03)

| Do | Don’t |
|----|-------|
| Agent service accounts with narrow IAM roles | Embed long-lived admin keys in the prompt or agent env |
| Short-lived tokens; audience-bound | Pass-through user OAuth to every tool by default |
| Separate “read corp wiki” from “send email” principals | One bot identity for all side effects |

### Human oversight (OWASP agent CS + AISVS themes)

| Requirement | Detail |
|-------------|--------|
| Risk-tiered autonomy | Auto-allow low; approve medium/high |
| Action preview | Human sees target, args, blast radius |
| Interrupt / rollback | User can stop mid-plan |
| Approval timeout fail-closed | No approval ⇒ **block**, do not proceed |
| Audit trail | Who approved what, when |

**Trust exploitation (ASI09):** fluent confidence ≠ correctness. Approval UIs must surface risk, citations, and uncertainty — not only polished prose.

### Unexpected code execution (ASI05)

| Ban / gate | Why |
|------------|-----|
| `eval` / shell from model text | Text → RCE |
| Unsafe deserialization from tool I/O | Classic exploit path |
| Unsandboxed code interpreters | Escape → host compromise |

Prefer sealed sandboxes with no secret mount, strict egress, and ephemeral FS.

### Budgets and cascading failure (LLM06 / ASI08)

| Budget | Example |
|--------|---------|
| Max tool calls / turn or /task | Stop loops |
| Max recursion / sub-agent depth | Swarm fan-out |
| Token and $ caps | Unbounded consumption |
| Wall-clock timeout | Hung agents |
| Circuit breaker | Trip when error rate or anomaly spikes |

### Kill switch and intermediate states

| Capability | Requirement |
|------------|-------------|
| **Manual kill switch** | Halt inference, streaming, **queued tool calls**, MCP sessions, workers — not only the chat UI |
| **Out-of-band channel** | Infrastructure/control plane — **not** mediated by the agent (AISVS / Five Eyes containment theme) |
| **Exercise regularly** | Documented drills with evidence |
| **Intermediate modes** | e.g. read-only; disable specific MCP; drop a retrieval source; smaller/safer model — without full outage |

```text
Full power → (trip) → Degraded (tools limited / read-only) → Kill (all stop)
                         ↑ human / SOC / platform control plane
```

### Logging for agents

Log: plan summary, tool name, args (redact secrets), policy decision, approver, budgets remaining, model/version. Feed SOC — Defensive-Ops owns SIEM depth.

## Apply

1. Inventory every tool; assign risk tier and identity.  
2. For the highest-risk tool, write policy rules in pseudocode (allow/deny/approve).  
3. Design approval UX fields for that tool.  
4. Define budgets (calls, $, time, depth) for one agent.  
5. Spec kill-switch scope (what must stop) and an intermediate “read-only” mode.  
6. Schedule a quarterly kill-switch drill checklist.  
7. Map three ASI IDs to tickets on your threat register ([1](./1_LLM_App_Threat_Model_In_Practice.md)).

### Self-check

- [ ] Explain model-proposes / code-disposes  
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
