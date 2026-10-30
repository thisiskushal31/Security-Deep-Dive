# DFD and STRIDE in practice

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

How to build a diagram that is honest enough to threaten, then walk STRIDE systematically — including Microsoft’s STRIDE-per-element map and a worked walkthrough. Workshops: [1](./1_Workshop_Process.md). Literacy: [Foundations/12](../Foundations/12_STRIDE_And_Threat_Modeling.md).

## Concept

### Data flow diagrams for threat modeling

A **DFD** answers “what are we working on?” Microsoft’s SDL approach uses four classic elements plus **trust boundaries**:

| Element | Symbol (classic) | Meaning |
|---------|------------------|---------|
| **Interactors** | Rectangle | People, external systems, servers *outside* your modeled process — data providers/consumers |
| **Process** | Circle (multi-process: concentric circles) | Computation you run |
| **Data store** | Two parallel lines | Files, DBs, queues, registry, buckets, secrets stores |
| **Data flow** | Directed arrow | Data in motion (network, RPC, pipes, bus…) |
| **Trust boundary** | Dotted line | Border between trusted and untrusted elements |

Trust is subjective and contextual: you might trust a bank with money and a mechanic with a car — not the reverse. On a diagram, a trust boundary means: **you do not fully trust what is on the other side**, and an attacker need not use your client or respect your protocol.

**Attack surface** is the set of reachable entry points. Trust boundaries organize the picture; attack surface is what you inventory and shrink.

```text
[Salesperson] ----HTTPS----> [Collection process] ----> [(Analysis DB)]
     interactor                    process                  data store
                              | trust boundary |
```

### Why the DFD quality dominates STRIDE quality

SEI and Microsoft both stress: inaccurate DFDs dictate how successful STRIDE will be. A pretty wrong diagram produces confident wrong threats.

### STRIDE as property violations

| Category | Violates | Microsoft-style description |
|----------|----------|-----------------------------|
| **Spoofing** | Authentication | Illegally access and use another user’s (or component’s) authentication information |
| **Tampering** | Integrity | Malicious modification of data at rest or in transit |
| **Repudiation** | Non-repudiation | Act while denying it, with no way for others to prove otherwise |
| **Information disclosure** | Confidentiality | Expose information to someone who should not have it |
| **Denial of service** | Availability | Deny service to valid users (make unavailable or unusable) |
| **Elevation of privilege** | Authorization | Unprivileged user gains privileged access sufficient to compromise or destroy the system |

Pointed questions to ask on the diagram (Microsoft TMT framing): How can an attacker change authentication data? What if they can read profile data? What if access to the profile database is denied?

## Advanced

### STRIDE is a mnemonic, not a taxonomy

STRIDE helps you remember categories of what can go wrong. It is a poor rigid taxonomy — threats overlap and cascade (spoof admin → then DoS). Use it as **prompts**, then write concrete, testable threat statements (“attacker replays bearer token from mobile logs to call `/admin`”), not just the letter “S”.

### STRIDE-per-element (the table you actually use)

Not every element is equally susceptible to every STRIDE category. Microsoft’s classic mapping:

| Element | S | T | R | I | D | E |
|---------|---|---|---|---|---|---|
| **Data flows** | | ✓ | | ✓ | ✓ | |
| **Data stores** | | ✓ | | ✓ | ✓ | |
| **Processes** | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| **Interactors** | ✓ | | ✓ | | | |

**How to use it:** for each element (or each trust-boundary crossing), only brainstorm the checked categories first. Then note cascading effects (spoofing the admin process enables DoS) without boiling the ocean tracking every chain.

**STRIDE-per-interaction** (later Microsoft variant): analyze threats on the *interaction* between two elements (e.g. User → API), which is what many tools highlight when you click a generated threat.

### DFD hygiene rules (before you STRIDE)

From Microsoft’s worked guidance plus drawing practice that keeps models honest:

| Rule | Failure mode if ignored |
|------|-------------------------|
| No magic data sources/sinks | DB that is never read = missing report reader / attacker path |
| Always a process between human and disk | “Psychokinesis” as transport — missing code that can be spoofed |
| Collapse similar elements *inside* one trust boundary | Forest lost in trees; duplicate threats |
| Context DFD + breakout diagrams | One giant diagram that lies about both sides of a boundary |
| Model the untrusted side as interactors | Pretending the client is trustworthy code you control |
| List files, registry, env, backups | Hidden stores that still hold secrets |
| **Label flows with data, not protocols** | “HTTPS” hides whether PII, tokens, or junk moves; attackers abuse possible data, not your intended verb |
| **Data flow ≠ control flow** | Triggering code ≠ moving information — DFDs track data |
| **Trust boundary around a lone node is usually noise** | Nodes already “trust themselves”; boundaries should separate *sets* of elements or document a real trust change (e.g. “runs in AWS account X”) |
| **Point boundaries to shield what you protect** | Ambiguous dotted lines that protect nothing |
| **Absence of a flow is a design claim** | If A cannot talk to B, what mechanism enforces that? Complex access control may deserve its own process node |
| **Most edges are bi-directional in practice** | One-way arrows often understate return channels (errors, tokens, metadata) |
| **Stay at context / layer-0–1 until stuck** | Implementation minutiae stalls workshops; expand a subgraph only when a boundary or attack/defense is ambiguous |
| **Put CI/CD, deploy, and update paths on the diagram** | Build agents, runners, deploy keys, and “who can hotfix prod” are attack surface — not an afterthought box off-page |

**Hardcore theory vs practice on internal flows:** a flow entirely inside a trust boundary is lower priority than an internet crossing — but “anything can fail,” remote DBs get mis-drawn as local, and defense in depth may still pay. Rank by exposure; do not pretend internal = zero risk.

### When the system includes AI / agents

STRIDE still covers classical trust-boundary issues (spoofed callers, tampered prompts/tools, disclosure of context, DoS on inference, elevation via tool abuse). It is **weak** alone on adversarial ML, data poisoning, goal hijack, and multi-agent collusion. Practice: finish STRIDE/DFD first, then deepen in [6 — AI and agentic threat modeling](./6_AI_And_Agentic_Threat_Modeling.md) (MAESTRO, OWASP LLM/ASI, ATLAS). Threat *literacy*: [Threats/8](../Threats/8_AI_Era_Threats.md). Build depth: [AI-Security](../AI-Security/README.md). Do not pretend STRIDE letters invent those threats.

### Assets on the diagram

Mark what an adversary **ultimately wants** (customer PII, funds movement, trade secrets, safety-critical actuation). Credentials, sessions, and admin APIs are often **stepping stones**, not the crown jewel — still model them, but do not confuse “steal password” with the business asset.

### Worked walkthrough (Fabrikam-style sales analyzer)

**Business ask:** collect accounting files from the sales force, compute on a central server, produce weekly reports.

**Latent security requirements** (derived, not customer-stated):

1. Protect data in transit and storage from disclosure  
2. Authenticate and authorize the sales force  
3. Survive malicious input (injection, overflows)  
4. Meet weekly availability for calculations  

**Better DFD shape:** treat clients as **external interactors** (attacker-controlled); focus the model on the server trust boundary; ensure a reader exists for the analysis DB (reports). Collapse collection+analysis into one process for modeling if they share technology and boundary — implementation may still split.

**Sample threats by element type:**

| Target | STRIDE | Threat sketch | Mitigation direction |
|--------|--------|---------------|----------------------|
| Flow: sales → collection | T, I, D | Modify or sniff files in transit; flood collection on quarter-end | TLS + authn; rate limits; integrity |
| Store: analysis DB | T, I, D | Insider alters bonuses; competitor reads; fill disk | AuthZ, encryption, monitoring, capacity |
| Process: collection | S, E, R | Fake collection endpoint; escalate via memory corruption; overwrite sales without audit | Mutual auth, hardening, append-only audit |
| Interactor: salesperson | S, R | Competitor poses as salesperson; deny submitting false data | Strong identity; non-repudiation logs |

When authentication for sales is undefined, record a **spoofing** threat even if it “feels like a requirements gap” — that *is* the threat model working.

### Ranking design threats

| Approach | Use |
|----------|-----|
| Likelihood × impact (local ordinal scale) | Default for design threats without a CVE |
| Include fix effort (optional) | When capacity is the bottleneck |
| CVSS | Known vulnerabilities — severity companion, not a TM method ([Vulnerability-Management/3](../Vulnerability-Management/3_Risk_Based_Prioritization_KEV_EPSS_SSVC.md)) |
| DREAD | Optional historical mnemonic — not required |

Expect moderately low false positives and moderately high false negatives with STRIDE alone (SEI citing Microsoft studies) — another reason for tester review and revisit, not one heroic pass.

### Data-centric modeling (NIST SP 800-154 draft)

When the thing you care about is **data**, add a data-centric pass (draft NIST guidance — principles, not a finished mandate):

1. **Identify and characterize** the system and data of interest (narrow: a logical data set on a host or small group)  
2. **Identify and select attack vectors** against storage, transmission, processing, input, and output locations (select by likelihood/impact when you cannot model all)  
3. **Characterize security controls** that mitigate those vectors (and how effective you believe they are)  
4. **Analyze the threat model** — residual risk, control gaps, decisions  

**Attack vector** (NIST draft sense): a segment of the pathway an attack uses — source of malicious content, potentially vulnerable processor, etc. An **attack model** is a scenario plus a path (one or more vectors). The set of attack models is the threat model; all vectors against a system are its **attack surface**.

### Cloud / hybrid adaptations

| Cloud reality | Diagram / STRIDE habit |
|---------------|------------------------|
| Shared responsibility | Draw provider-managed vs customer-configured edges |
| IAM roles, VNets, buckets, managed DBs | First-class stores and interactors |
| Ephemeral containers / serverless | Processes come and go — trust who deploys them |
| Multi-tenant / federation | Spoofing and elevation across tenants |
| Data residency | Disclosure and compliance on egress flows |

### Entry points for elicitation beyond pure STRIDE

| Technique | When |
|-----------|------|
| Abuse / misuse cases | Valid workflows out of order, repeated, concurrent, or for unintended outcomes |
| CAPEC / ATT&CK-informed prompts | After the diagram is stable — [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md) |
| Brainstorm then formalize | Mixed technical/non-technical rooms (OWASP) |

## Apply

1. Draw a context DFD for one service; mark every trust boundary; list assumptions.  
2. Apply DFD hygiene: kill magic sinks; ensure every store has a reader/writer process or interactor.  
3. Build a STRIDE-per-element matrix for your diagram (copy the susceptibility table; fill concrete threats).  
4. Rank top five with likelihood × impact; write mitigate vs accept for each.  
5. For one sensitive data type, run the four SP 800-154 draft steps in half a page.  
6. Have a tester or outsider mark three “missing from diagram” items before you call Q1 done.

### Self-check

- [ ] Draw all five DFD building blocks from memory  
- [ ] Recite which STRIDE categories apply to flows vs processes vs interactors  
- [ ] Explain why clients should often be interactors, not trusted processes  
- [ ] Give one example of a latent security requirement pulled from a business ask  
- [ ] Contrast STRIDE-per-element with ranking by CVSS  
- [ ] State the four data-centric steps (draft NIST)  
- [ ] Explain why flow labels should name data, not only TLS  
- [ ] Contrast an asset with a stepping-stone credential  

## Next

- [PASTA staged walkthrough](./3_PASTA_Staged_Walkthrough.md)
- AI / agents on the diagram → [6](./6_AI_And_Agentic_Threat_Modeling.md)

## Further reading

- [Microsoft Learn — Threats (STRIDE)](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats)
- [Microsoft Learn — Uncover Security Design Flaws Using The STRIDE Approach](https://learn.microsoft.com/en-us/archive/msdn-magazine/2006/november/uncover-security-design-flaws-using-the-stride-approach)
- [Microsoft — SDL Threat Modeling](https://www.microsoft.com/en-us/securityengineering/sdl/threatmodeling)
- [OWASP — Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [NIST SP 800-154 (draft) — Data-Centric System Threat Modeling](https://csrc.nist.gov/pubs/sp/800/154/ipd)
- [SEI — Threat Modeling Methods Summary](https://insights.sei.cmu.edu/library/threat-modeling-a-summary-of-available-methods/)
