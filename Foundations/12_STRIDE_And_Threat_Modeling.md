# STRIDE and threat modeling

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Threat modeling is a structured way to find what can go wrong in a design before you ship a new trust boundary. The goal is prioritized mitigations with owners — not a diagram for its own sake.

Architecture worked examples: [System-Design Security-Tradeoffs/1](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security-Tradeoffs/1_Threat_Modeling_At_Design_Time.md). SDLC placement: [AppSec/1](../AppSec/1_OWASP_And_Secure_SDLC.md). Attacker-centric models: [18](./18_Adversary_Models_Kill_Chain_And_ATTCK.md). Workshop / method depth beyond literacy: [Threat-Modeling](../Threat-Modeling/README.md) (stub).

## Concept

## Four questions every model answers

| Question | Typical activity |
|----------|------------------|
| What are we working on? | Diagram / DFD; assets; actors |
| What can go wrong? | STRIDE, attack trees, abuse cases, ATT&CK-informed prompts |
| What are we going to do about it? | Mitigate, eliminate, transfer, or accept — with owners |
| Did we do a good enough job? | Review, tests, revisit when boundaries change |

## When to model

| Situation | Depth |
|-----------|--------|
| Small internal change; no new data class; no new trust boundary | Short checklist on the design note or PR |
| New public API, personal data, payments, or partner integration | Facilitated session and written mitigations |
| Major architecture change | Full model plus design trade-off review and verification gates |

## Trust boundary vs attack surface

| Term | Meaning |
|------|---------|
| **Trust boundary** | Where the level of trust changes (internet → edge → service → store; admin plane separate) |
| **Attack surface** | The set of reachable entry points an adversary can try (APIs, ports, forms, upload paths, identity endpoints, physical ports) |

Trust boundaries organize the diagram. Attack surface is what you inventory and shrink. Crossing a boundary without authentication, authorization, integrity protection, or rate limits is a common design failure.

## Inputs

1. A diagram of components and data flows (even a rough one).  
2. **Trust boundaries** marked on that diagram.  
3. **Data classes** on stores and egress ([1](./1_CIA_And_Risk.md)).  
4. **Actors** who can touch each boundary (user, admin, automation, vendor, insider).

```text
[User] --HTTPS--> [Edge/API] --private--> [Service] --private--> [Data store]
                      |                       |
                 trust boundary          trust boundary
```

Every arrow that crosses a boundary is a candidate for spoofing, tampering, disclosure, or denial of service.

## STRIDE

STRIDE groups threats so teams ask pointed questions about the design:

| Category | Description |
|----------|-------------|
| **Spoofing** | Illegally accessing and then using another user’s (or component’s) authentication information |
| **Tampering** | Malicious modification of data — at rest or in transit |
| **Repudiation** | Performing an action while denying it, with no way for others to prove otherwise. Non-repudiation is the ability to counter that threat |
| **Information disclosure** | Exposure of information to someone who should not have access |
| **Denial of service** | Deny service to valid users (for example make a service unavailable or unusable) |
| **Elevation of privilege** | An unprivileged user gains privileged access and can compromise or destroy the system |

| Letter | Question on the diagram |
|--------|-------------------------|
| S | Can a caller forge identity? |
| T | What integrity protection exists on this data or path? |
| R | Can we prove who did what? |
| I | Where can sensitive data leak? |
| D | What is the cheapest way to exhaust us? |
| E | Where can trust jump upward? |

### Common building blocks

| Component | Example STRIDE concerns |
|-----------|-------------------------|
| API gateway | Spoofed clients; missing access logs; cheap floods; exposed admin routes |
| App service | Stolen sessions; over-fetched personal data; missing object-level authorization |
| Queue | Spoofed producers; poisoned messages; sensitive payloads in dead-letter logs |
| Cache | Poisoned entries; secrets in keys/values; shared-tenant key collision |
| Database | Stolen credentials; unauthorized writes; over-broad read roles |
| CI pipeline | Stolen deploy identity; malicious workflow; secrets in logs |

## Ranking and scoring companions

| Tool | Role |
|------|------|
| Likelihood × impact (from [1](./1_CIA_And_Risk.md)) | Default prioritization for design threats |
| **DREAD** (Damage, Reproducibility, Exploitability, Affected users, Discoverability) | Older mnemonic some courses still teach for ranking; treat as optional awareness, not a required process |
| **CVSS** | Severity score for **vulnerabilities** (scanner/NVD language). Companion to triage, not a threat-modeling method by itself |

Use CVSS when comparing known CVEs or verified vulns. Use likelihood × impact (or a documented local scale) when ranking design threats that do not yet have a CVE.

## Advanced

## Other methods (awareness)

| Method | Role |
|--------|------|
| Attack trees | Decompose how an attacker reaches a goal |
| PASTA | Heavier, risk-centric staged process tying tech threats to business impact |
| LINDDUN | Privacy-oriented threat categories |
| Abuse / misuse cases | Misuse stories next to user stories |
| VAST | Agile-scale habit: separate application and operational models across many teams |
| OCTAVE | Organization / asset-centric risk evaluation — enterprise precursor more than per-app STRIDE |
| Trike | Risk-management / audit-oriented framing |

| When you need | Prefer |
|---------------|--------|
| Security threats on a technical design | STRIDE on the DFD |
| One high-value attacker goal | Attack tree |
| Personal-data / privacy harms | LINDDUN (with or after STRIDE) |
| Business-impact-driven stages | PASTA |
| Org-wide critical-asset risk | OCTAVE, then zoom into apps with STRIDE |
| Scale across many agile teams | VAST-style lightweight models in the workflow |

Pick one habit that produces mitigations before launch. You do not need every method on every change.

## Apply
1. List threats; score with [1](./1_CIA_And_Risk.md) (and CVSS when scoring known vulns).  
2. Map high items to control types ([2](./2_Controls_And_Physical.md)).  
3. Put preventive controls in design and code; detective controls in logs and monitoring.  
4. Verify with authorization tests and delivery gates in [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).  
5. Revisit when trust boundaries change ([9](./9_Change_Management_And_Security.md)).

## Anti-patterns

| Anti-pattern | Better |
|--------------|--------|
| One workshop, never again | Revisit on new trust boundaries |
| Network threats only | Include abuse, fraud, insider, and automation |
| Mitigations with no owner | Ticket, name, date |
| Model as theater | Tie findings to tests and gates |
| CVSS-only backlog for design threats | Separate design ranking from CVE severity |

## Self-check

- [ ] Answer the four threat-modeling questions for a system you know  
- [ ] Mark trust boundaries and name three attack-surface entry points on a four-box system  
- [ ] Write one mitigation for each STRIDE letter on that diagram  
- [ ] Explain when STRIDE, PASTA, OCTAVE, or an attack tree fits better  
- [ ] Explain why CVSS is a scoring companion, not a full threat model  

## Next

- [Zero Trust and deception basics](./13_Zero_Trust_And_Deception_Basics.md)

## Further reading

- [Microsoft Learn — Threats (STRIDE) and the Threat Modeling Tool](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats)
- [OWASP Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [OWASP Threat Modeling](https://owasp.org/www-community/Threat_Modeling)
- [OWASP Threat Dragon](https://github.com/OWASP/threat-dragon)
- [FIRST — Common Vulnerability Scoring System (CVSS)](https://www.first.org/cvss/)
