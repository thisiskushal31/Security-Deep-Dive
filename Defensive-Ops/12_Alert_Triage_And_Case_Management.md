# Alert triage and case management

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Most SOC (Security Operations Center) hours are spent here: **decide what an alert means**, enrich it, and leave a case trail another human can continue. Detection engineering creates the signal ([4](./4_Detection_Engineering_And_ATTCK.md)); triage turns signal into IR (incident response) or a clean close ([7](./7_Incident_Response_Lifecycle.md)).

## Concept

### Triage questions (every alert)

| Question | If unclear |
|----------|------------|
| Is the alert **valid** (true positive / benign true / false)? | Need more telemetry — do not guess |
| Is it **actionable now**? | Park with SLA (service level agreement), not silent drop |
| What is the **blast radius** (user, host, data)? | Scope before containment theater |
| Who **owns** the asset? | Enrich from inventory / CMDB (configuration management database) |
| Does this become an **incident**? | Escalate with facts, not vibes |

```text
Alert
  │
  ├─ enrich (identity, host, geo, intel, ticket history)
  ├─ classify (TP / BTP / FP / duplicate)
  ├─ action (close · monitor · escalate · contain)
  └─ record (case note another shift can trust)
```

### Case management is not bureaucracy

| Artifact | Why |
|----------|-----|
| Timeline of facts vs hypotheses | Stops rumor drift across shifts |
| Linked evidence (query IDs, PCAP (packet capture) paths, EDR (Endpoint Detection and Response) IDs) | Review and legal readiness |
| Decisions + who approved containment | Authority trail |
| Close reason codes | Feeds detection tuning ([4](./4_Detection_Engineering_And_ATTCK.md)) |

## Advanced

### Tiered handling (common shape)

| Tier | Typical work |
|------|----------------|
| **L1** | Validate, enrich, known-good closes, escalate unknowns |
| **L2** | Deep investigation, hunting pivots, draft containment |
| **L3 / IR** | Major incidents, forensics, cross-team command |

Titles vary; the handoff contract matters more than the label. Burnout often means L1 is drowning in unowned false positives — fix detections, do not only hire.

### Enrichment minimum viable set

| Field | Source habit |
|-------|--------------|
| User / service identity | IdP (Identity Provider) |
| Host / IP ownership | Inventory, DHCP, cloud tags |
| Recent similar alerts | Case search |
| External intel hit? | CTI (Cyber Threat Intelligence) match — [Threat-Intelligence](../Threat-Intelligence/README.md) |
| Business criticality | Asset tier |

Automation can fetch these — boundaries in [13](./13_Automation_And_SOAR_Boundaries.md). Decode / transform blobs during triage: [CyberChef](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/CyberChef) (prefer local/trusted for sensitive data).

### Close codes that help engineering

| Code | Meaning |
|------|---------|
| True positive — incident | Became IR |
| True positive — expected | Allowed admin / scanner; tune allowlist with expiry |
| Benign true positive | Real activity, not malicious (backup job looked like ransomware prep) |
| False positive — logic | Rule bug |
| False positive — telemetry | Bad parse / wrong field |
| Duplicate | Already tracked under case X |

### Shift handoff

Write the next analyst’s first five minutes: current theory, next query, what not to do (e.g. “do not reimage yet”). Cases without handoff notes force full re-investigation.

## Apply

1. Document your L1→L2 escalate criteria in ten lines.  
2. Add close-reason codes to your case tool (or spreadsheet).  
3. Pick your noisiest alert; classify last 20 fires with those codes.  
4. Write one enrichment checklist every L1 must complete before close.

## Further reading

- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [FIRST — CSIRT Services Framework](https://www.first.org/standards/frameworks/csirts/csirt_services_framework_v2.1)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [CISA — Incident and Vulnerability Response Playbooks](https://www.cisa.gov/resources-tools/resources/federal-government-cybersecurity-incident-and-vulnerability-response-playbooks)
