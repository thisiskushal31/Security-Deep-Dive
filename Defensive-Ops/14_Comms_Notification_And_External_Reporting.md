# Comms, notification, and external reporting

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Incidents fail in the **messaging layer** as often as in the technical layer: wrong people, late regulators, panicked customers, or chat sprawl with no single source of truth. IR lifecycle: [7](./7_Incident_Response_Lifecycle.md). Legal/privacy clocks also appear in [Foundations](../Foundations/README.md) / [GRC](../GRC/README.md) — this note is the ops wiring.

## Concept

### Audiences (separate templates)

| Audience | Need |
|----------|------|
| **On-call / IR team** | Facts, hypotheses, next actions |
| **System owners** | Impact and required actions on their systems |
| **Exec / crisis** | Business impact, options, decisions needed |
| **Customers / users** | Clear, non-speculative status when policy says so |
| **Regulators / authorities** | Mandatory content and deadlines |
| **Partners / processors** | Contractual notify obligations |

```text
Incident declared
      │
      ├─ internal bridge (single channel + commander)
      ├─ exec brief cadence
      ├─ customer / public (if triggered)
      └─ regulator / authority (if triggered)
```

### Single source of truth

Pick one incident channel or case as authoritative. Side DMs create conflicting “truth.” Comms lead (can be dual-hat) owns outbound wording; incident commander owns technical decisions — [1](./1_Defensive_Ops_And_SOC_Shape.md).

## Advanced

### Notification triggers (write them down)

| Trigger example | Clock starts when… |
|-----------------|-------------------|
| Personal data likely accessed | Criteria in privacy / counsel playbook |
| Confirmed ransomware on crown-jewel segment | Sev definition |
| Cloud key used from unexpected geo + data access | Pre-agreed pattern |
| Supplier notifies you of their breach | Contract clause |

Do not invent legal advice here — bind clocks to counsel-approved matrices.

### Holding statements

Pre-draft:

| Type | Trait |
|------|-------|
| Internal ack | “We are investigating; next update at T+N” |
| Customer holding | No unproven blame; no speculative root cause |
| Final | Facts, impact, what changed |

### TLP and sharing

When sharing with peers or ISACs (Information Sharing and Analysis Centers), label sensitivity (e.g. FIRST TLP (Traffic Light Protocol)). Oversharing IOCs (indicators of compromise) into public chat can tip adversaries; undersharing can leave peers blind. CTI handoff habits: [Threat-Intelligence](../Threat-Intelligence/README.md).

### Evidence vs narrative

External updates must not invent certainty. Separate: confirmed facts, working theory, open questions. Case notes hold the detail ([12](./12_Alert_Triage_And_Case_Management.md)); external notes stay short.

## Apply

1. Build a one-page audience × template matrix for Sev-1.  
2. Name comms lead vs incident commander for after-hours.  
3. Confirm regulator / counsel contacts and last reviewed date.  
4. Practice a 15-minute exec brief from a tabletop ([15](./15_Tabletop_And_Purple_Exercises.md)).

## Further reading

- [CISA — Incident and Vulnerability Response Playbooks](https://www.cisa.gov/resources-tools/resources/federal-government-cybersecurity-incident-and-vulnerability-response-playbooks)
- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [FIRST — Traffic Light Protocol (TLP)](https://www.first.org/tlp/)
- [CISA — Cybersecurity Performance Goals](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals)
