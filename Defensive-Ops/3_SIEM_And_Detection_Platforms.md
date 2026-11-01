# SIEM and detection platforms

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

A SIEM (Security Information and Event Management) platform aggregates security-relevant events so people can detect and investigate abuse. The product name matters less than **sources, use cases, ownership, and response paths**. Telemetry prerequisites: [2](./2_Telemetry_And_Log_Management.md). Detection quality: [4](./4_Detection_Engineering_And_ATTCK.md).

## Concept

### Jobs a SIEM (or equivalent stack) must do

| Job | Practice |
|-----|----------|
| **Collect** | Auth, endpoint, cloud audit, network, app, email |
| **Normalize / parse** | Same fields for user, src_ip, action, outcome |
| **Detect** | Rules, correlations, UEBA (User and Entity Behavior Analytics), intel matches |
| **Investigate** | Pivot timelines; enrich with asset and identity ownership |
| **Report** | Coverage and response metrics that leadership can act on |

```text
Sources ──▶ ingest / parse ──▶ store
                                │
                    ┌───────────┼───────────┐
                    ▼           ▼           ▼
                 alerts      hunt UI     case / ticket
                    │
                    ▼
              on-call + IR ([7](./7_Incident_Response_Lifecycle.md))
```

### What a SIEM is not

| Not a SIEM’s job alone | Better home |
|------------------------|-------------|
| Product APM (Application Performance Monitoring) metrics | Observability / DevOps monitoring |
| Authoritative asset inventory | CMDB (configuration management database) / cloud inventory |
| Vulnerability prioritization program | [Vulnerability-Management](../Vulnerability-Management/README.md) |
| Full packet decode lab | [Tooling / Networks](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive) |

Observability stacks and SIEMs can share pipelines; security detections still need **security-relevant** fields and an IR path.

### Lab platform (install elsewhere)

Open-source SIEM/XDR stack for hands-on labs: [Wazuh](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wazuh) (agents + server, indexer, dashboard). Use it to practice the jobs above — not as a substitute for ownership, runbooks, or IR. Other commercial stacks fit the same program habits; this repo does not re-home every vendor UI.

## Advanced

### Use-case driven deployment

| Anti-pattern | Better pattern |
|--------------|----------------|
| “Ingest everything, write rules later” | Top 10 detections tied to critical assets first |
| One mega-rule that never fires cleanly | Small, testable detections with owners |
| Permanent mute of noisy truth | Suppressions with expiry and ticket |
| Alert with no enrichment | User, host, owner, criticality on the ticket |

### Correlation and context

Useful pivots:

| Pivot | Example |
|-------|---------|
| Identity | Same user across IdP (Identity Provider), VPN, SaaS |
| Host | Process → network → auth on one endpoint |
| Campaign | Shared IOC (indicator of compromise) across mail and web |

Threat intel feeds help when **matched to your assets** — CTI product depth: [Threat-Intelligence](../Threat-Intelligence/README.md).

### Baselines and UEBA (User and Entity Behavior Analytics)

| Idea | Use | Failure mode |
|------|-----|--------------|
| Peer baselines | Spot odd admin on a finance laptop role | Bad peer groups → noise |
| Time baselines | After-hours mass downloads | Timezone / on-call blindness |
| Entity risk scores | Prioritize triage | Opaque scores nobody trusts |

UEBA helps when **identity and asset context** are clean. Garbage CMDB (configuration management database) in → expensive anomalies out. Pair with triage discipline ([12](./12_Alert_Triage_And_Case_Management.md)).

### On-call and cases

| Habit | Why |
|-------|-----|
| Severity definitions written down | Stops random escalation |
| Case = evidence + timeline + decisions | Handoffs survive shift changes |
| After-hours path tested | Pages that go nowhere are worse than silence |

Automation (enrich, open ticket, notify) can use workflow tools — install home: [Tooling n8n](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Automation/n8n). Boundaries: [13](./13_Automation_And_SOAR_Boundaries.md). Keep playbook logic reviewable; do not hide IR decisions only in opaque graphs.

### Capacity planning

| Signal | Action |
|--------|--------|
| Alerts per analyst >> workable | Tune / suppress with expiry; hire is last |
| Queue age rising | Divert hunts; fix noisy rules first |
| Enrichment time dominates | Automate safe enrichments ([13](./13_Automation_And_SOAR_Boundaries.md)) |

## Apply

1. Name your detection platform (SIEM, XDR (Extended Detection and Response), or “searchable log store + rules”).  
2. List five live use cases with an owner each.  
3. Pick one noisy alert: add expiry to its suppression or fix the rule.  
4. Trace one alert from fire → human → ticket fields present/missing.  
5. Note one baseline (user or host peer group) you could define this month.

## Further reading

- [NIST SP 800-92 — Log Management](https://csrc.nist.gov/pubs/sp/800/92/final)
- [NIST CSF 2.0 — Detect](https://www.nist.gov/cyberframework/detect)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [Tooling — Wazuh](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wazuh)
- [Wazuh documentation](https://documentation.wazuh.com/current/index.html)
