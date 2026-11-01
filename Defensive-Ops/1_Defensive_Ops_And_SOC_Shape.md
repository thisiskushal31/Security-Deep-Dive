# Defensive ops and SOC shape

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Defensive operations turn signals into decisions: notice abuse early, investigate with enough context, contain damage, and change systems so the same path fails next time. A SOC (Security Operations Center) is one common shape for that work — not the only one. Smaller teams still need the same **loop**, even without a branded “SOC room.”

CSF (Cybersecurity Framework) 2.0 frames outcomes under Detect, Respond, and Recover (with Govern / Identify / Protect feeding them). IR (incident response) depth: [7](./7_Incident_Response_Lifecycle.md).

## Concept

### The loop that matters

```text
Telemetry ──▶ Detect ──▶ Triage / investigate
                              │
                              ▼
                         Respond / recover
                              │
                              ▼
                    Lessons → better telemetry & detections
```

| Stage | Question |
|-------|----------|
| Telemetry | Would we *see* this if it happened? |
| Detect | Would an alert or hunt find it in time? |
| Investigate | Can we scope systems, identities, and data? |
| Respond | Who decides containment, and how fast? |
| Improve | Did a detection, control, or runbook change? |

Buying a SIEM (Security Information and Event Management) without that loop produces dashboards, not defense. Telemetry first: [2](./2_Telemetry_And_Log_Management.md).

### SOC vs CSIRT vs “everyone on-call”

| Shape | Focus |
|-------|-------|
| **SOC** | Continuous monitoring, triage, escalation |
| **CSIRT (Computer Security Incident Response Team)** | Coordinated incident handling services for constituents |
| **Embedded / dual-hat** | Platform or AppSec (application security) engineers share IR duty |

FIRST’s CSIRT Services Framework describes *services* a team may offer (service design, information security event management, incident management, vulnerability management, situational awareness, knowledge transfer — teams pick what their mandate needs). You do not need every service on day one.

### What “good” looks like (timeless)

| Signal of health | Signal of theater |
|------------------|-------------------|
| Named owners for critical alerts | Alerts with no runbook |
| Playbooks tested in tabletop or purple labs | Binders nobody opens |
| Coverage mapped to assets that matter | ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) posters with empty telemetry |
| Clear escalate path to legal / exec | “Page security@” as the only plan |

## Advanced

### Constituents and mandate

Before tools, answer:

| Question | Why it shapes the team |
|----------|------------------------|
| Who are we defending (employees, customers, OT (operational technology))? | Scope of monitoring |
| What must we report externally? | Notification clocks |
| What can we take offline without a VP? | Containment authority |
| What is outsourced (MDR (Managed Detection and Response), MSSP (Managed Security Service Provider))? | Handoff contracts |

### Service catalog (pick deliberately)

| Service class (examples) | Typical outcomes |
|--------------------------|------------------|
| Event / alert management | Triage, enrichment, close or escalate |
| Incident management | Coordinated response for confirmed incidents |
| Vulnerability management coordination | Urgent vuln response — program depth in [Vulnerability-Management](../Vulnerability-Management/README.md) |
| Situational awareness | Threat context for *your* assets — CTI (Cyber Threat Intelligence) depth in [Threat-Intelligence](../Threat-Intelligence/README.md) |
| Knowledge transfer | Training, tabletop, purple exercises |

### Operating model choices

| Model | Fits when |
|-------|-----------|
| In-house SOC 24×7 | High change rate, regulated uptime, budget for shifts |
| Follow-the-sun / shared SOC | Global org, strong handoff discipline |
| Hybrid + MDR (Managed Detection and Response) | Need depth overnight; keep IR authority in-house |
| Virtual SOC (engineers on-call) | Early stage — still needs playbooks and telemetry |

Outsourcing detection does **not** outsource accountability for containment decisions or customer notification ([14](./14_Comms_Notification_And_External_Reporting.md)).

### Analyst tiers (wire to triage)

| Tier | Owns |
|------|------|
| L1 | Validate, enrich, known closes — [12](./12_Alert_Triage_And_Case_Management.md) |
| L2 | Deep investigation, hunt pivots, draft containment |
| L3 / IR lead | Major incidents, forensics, cross-team command |

### Purple without a separate org chart

Agree technique IDs, run an authorized exercise, prove a detection or ticket path, then fix gaps. Exercise design: [15](./15_Tabletop_And_Purple_Exercises.md). Metrics: [11](./11_Metrics_Purple_And_Improvement.md). Offense phases: [Offensive](../Offensive/README.md).

### Tooling map (do not duplicate installs)

| Job | Home |
|-----|------|
| Packet / memory lab tools | [Tooling Defensive](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive) |
| Workflow automation | [Tooling n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n) |
| Recon scanners (purple noise) | [Tooling Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance) |

## Apply

1. Draw your current loop (even if it is “Slack + one person”).  
2. List five asset classes that must be visible (IdP (Identity Provider), email, cloud audit, endpoints, edge).  
3. Name the decision maker for “isolate a production host.”  
4. Pick one CSIRT-style service you will *not* offer yet — and who covers the gap.  
5. Write L1 vs L2 escalate criteria in five bullets.

## Further reading

- [NIST Cybersecurity Framework (CSF) 2.0](https://www.nist.gov/cyberframework)
- [FIRST — CSIRT Services Framework](https://www.first.org/standards/frameworks/csirts/csirt_services_framework_v2.1)
- [CISA — Cross-Sector Cybersecurity Performance Goals](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals)
- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
