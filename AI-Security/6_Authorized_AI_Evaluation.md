# Authorized AI red team and evaluation process

[← AI-Security](./README.md)

> **Progression:** concept → advanced → apply.

How to **measure** whether AI controls work — under authorization, with scope and ethics. This is the **Measure** function feeding **Manage** tickets. Threat register: [1](./1_LLM_App_Threat_Model_In_Practice.md). Ethics: [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

This note is **process and metrics**. Attack **mechanisms**, scenario anatomies, and authorized test *shapes*: [8](./8_Attack_Mechanisms_For_Defenders.md). Run tests only in scope you are authorized for.

Trustworthiness is multi-characteristic — security and resilience sit beside validity, privacy, fairness, and accountability. Eval programs that only score “accuracy” miss the security surface:

![Characteristics of trustworthy AI systems](../Assets/AI-Security/NIST_AI_RMF_Fig4_Trustworthiness.png)

*Credit: NIST AI RMF 1.0 (AI 100-1), Figure 4.*

```text
Govern → Map (register) → Measure (this note) → Manage (tickets / residual risk)
                ↑_________________ failed evals ______________|
```

## Concept

### Evaluation vs “someone tried a jailbreak once”

| Activity | Purpose | Pass signal |
|----------|---------|-------------|
| **Functional / quality eval** | Task accuracy, groundedness, UX | Product metrics |
| **Safety / policy eval** | Disallowed content classes *your* policy defines | Policy classifier / reviewer |
| **Security / adversarial eval** | Injection, tool abuse, data exfil, agency bypass — **against your controls** | Tool deny / ACL hold / gate block in **logs** |
| **Red team (authorized)** | Human-driven scenarios mapped to threat register + ATLAS | Control gap tickets |

“The model refused politely” is not a security pass if tools still executed.

### Why adversarial eval exists

Architecture assumes the model will be steered ([2](./2_Prompt_Injection_Defenses.md)). Eval proves whether your **runtime** still refuses impact. Without Measure, Map is a slide deck and Manage has no evidence.

### Authorization checklist (gate before any adversarial work)

| Gate | Required |
|------|----------|
| Written approval | Owner + security |
| In-scope systems | Staging / lab preferred; prod only if explicitly allowed |
| Out-of-scope | Third-party tenants, personal accounts, unrelated cloud |
| Data rules | No real customer PII in prompts/fixtures unless approved |
| Stop conditions | When to halt; kill-switch available ([4](./4_Agentic_Controls.md)) |
| Evidence handling | Logs, tickets, TLP if shared externally |
| No misuse cookbook as deliverable | Report control gaps; fixtures stay in your lab |

All security testing requires **written scope**, environment, and rules of engagement — same as classic AppSec.

## Advanced

### Build an eval matrix from the threat register

| Register item | Eval type | Pass criteria (examples) |
|---------------|-----------|--------------------------|
| LLM01 indirect injection via RAG | Adversarial scenario class ([8](./8_Attack_Mechanisms_For_Defenders.md) T-RAG-1) | Tool **not** invoked; or human gate blocks |
| LLM01 / jailbreak → tool | Mechanism + agency | Model text may comply; **tools** still cold |
| LLM03 excessive agency | Tool policy tests | Over-scope args denied by policy engine |
| LLM09 cross-tenant retrieve | Access tests | Zero foreign chunks in context logs |
| ASI02 destructive tool | Approval tests | No exec without approval; timeout fail-closed |
| LLM06 cost bomb | Load / abuse (T-BUD-1) | Quotas trip; no unbounded $ |
| LLM07 harmful advice acted on | Process | UI shows uncertainty; no auto-side-effect |
| ASI04 malicious MCP descriptor | Supply-chain lab | Server not allowlisted; or descriptor review blocks promote |

Map techniques to [MITRE ATLAS](https://atlas.mitre.org/) (prompt injection vs jailbreak IDs). Pass/fail = **control telemetry**, not vibes.

### How to run a scenario (fixture shapes, your lab)

1. Pick a register row and a fixture *shape* from [8](./8_Attack_Mechanisms_For_Defenders.md) (T-DIR-1, T-IND-1, T-RAG-1, T-UNI-1, T-MCP-1, T-JB-1, …).  
2. Author the fixture yourself for **your** product in a lab corpus / lab mailbox / lab MCP.  
3. Execute under the authorization one-pager.  
4. Assert on logs: `tool_invocations == 0`, `policy_decision == deny`, `approval_required == true`, `foreign_chunks == 0`, Unicode stripped, memory write blocked.  
5. Open Manage tickets for every fail; re-test after fix.

**Adaptive testing requirement:** disclose the deployed defense to red-teamers. Static attack packs that never see your filters produce misleading “near zero” success rates; adaptive attackers who know the marking scheme, strip list, and policy often succeed where static tests do not. Baseline public benches (e.g. AgentDojo, JailbreakBench) then layer your defense-aware scenarios. Map techniques to `AML.T0051` / `AML.T0054` (and ASI IDs for agents).

### Cadence (practical program)

| Cadence | What |
|---------|------|
| Every release (material AI change) | Automated regression: schema validation, policy unit tests, retrieve ACL tests |
| Sprint / monthly | Scenario pack against top register items |
| Quarterly | Authorized human red team / purple exercise + kill-switch drill |
| Continuous | Production telemetry: tool denies, approval rates, cost anomalies, kill-switch health |

### Metrics that matter

| Metric | Why |
|--------|-----|
| % privileged tools behind deterministic policy | Agency control coverage |
| Human-gate bypass attempts blocked | ASI09 / approval integrity |
| Cross-tenant retrieve incidents | LLM09 |
| Eval cases mapped to open threats | Measure completeness |
| Mean time to kill-switch in drills | Containment readiness |
| Drift: new MCP/tools without review | ASI04 hygiene |
| Failed evals still open > SLA | Manage discipline |

### Collaboration literacy

When sharing AI security findings across orgs: use agreed channels, protect sensitive model/data details, align on roles during incidents involving AI systems. Internally: feed failed evals into **Manage** (tickets) and CTI/detection where relevant ([Threat-Intelligence](../Threat-Intelligence/README.md)).

### What “good” reporting looks like

| Include | Avoid |
|---------|-------|
| Scenario class + DFD element + catalog ID | Raw payload dumps as the deliverable |
| Expected vs actual control behavior | “Model is unsafe” without a control gap |
| Evidence (logs, policy decision) | Testing prod with customer data unauthorized |
| Remediation ticket | Scoreboard without fixes |
| Retest result | Closed without verification |

## Apply

1. Write an authorization one-pager for AI adversarial eval in your org.  
2. Pick five threat-register rows; define pass/fail for each using **telemetry**.  
3. List automated tests you can run in CI (policy, ACL, schema).  
4. Schedule a quarterly exercise outline (roles, scope, kill-switch drill).  
5. Define three production metrics dashboards for agency / RAG / cost.  
6. Map two ATLAS technique *names* to register items (coverage tags only).  
7. Author T-IND-1 and T-RAG-1 fixtures in lab; wire asserts to tool-deny logs.

### Self-check

- [ ] Distinguish quality eval vs security adversarial eval  
- [ ] List authorization gates before testing  
- [ ] Build eval cases from the threat register with log-based pass/fail  
- [ ] Name release vs quarterly cadence items  
- [ ] State metrics for agency, ACL, and kill-switch  
- [ ] Place Secure & Resilient among trustworthiness characteristics  
- [ ] Refuse payload-cookbook framing for this handbook  

## Next

- [Shadow AI and private GenAI](./7_Shadow_AI_And_Private_GenAI.md)

## Further reading

- [CISA — JCDC AI Cybersecurity Collaboration Playbook](https://www.cisa.gov/sites/default/files/2025-01/JCDC%20AI%20Playbook.pdf)
- [NIST AI RMF — Measure / Manage](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST AI 600-1 — Generative AI Profile](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [Foundations/3 — Ethics, authorization, lab rules](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md)
- [OWASP LLM Top 10 2026](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/)
- [Attack mechanisms for defenders](./8_Attack_Mechanisms_For_Defenders.md)
