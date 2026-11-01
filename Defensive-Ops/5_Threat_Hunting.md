# Threat hunting

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Threat hunting is **hypothesis-driven** search for attacker behavior that existing alerts may have missed. It is not “staring at dashboards until something feels weird,” and it is not a substitute for IR (incident response) when a confirmed incident is already burning. Detection engineering: [4](./4_Detection_Engineering_And_ATTCK.md). IR: [7](./7_Incident_Response_Lifecycle.md).

## Concept

### Hunt vs alert vs IR

| Mode | Trigger | Outcome |
|------|---------|---------|
| **Alert-driven** | Rule / model fires | Triage → close or escalate |
| **Hunt** | Hypothesis (“if X, we should see Y”) | Find unknown-unknowns; feed new detections |
| **IR** | Suspected/confirmed incident | Contain, eradicate, recover |

```text
Hypothesis ──▶ scope data ──▶ query / pivot
                                  │
                    ┌─────────────┼─────────────┐
                    ▼             ▼             ▼
                  clean         lead          incident
                    │             │             │
                    ▼             ▼             ▼
               document      new detection    hand to IR
```

### Good hypotheses

| Weak | Stronger |
|------|----------|
| “Look for malware” | “If ransomware prep occurs, we should see unusual mass file renames + backup deletion attempts on servers in scope” |
| “Check all PowerShell” | “Encoded PowerShell spawned by Office on finance endpoints this week” |
| “Browse threat feeds” | “Technique T1059.001 against our ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) priority list using *our* process telemetry” |

Hunts need **telemetry depth** ([2](./2_Telemetry_And_Log_Management.md)) and a time box so they do not become endless scrolling.

## Advanced

### Hunt loop

1. Pick technique / risk relevant to your assets.  
2. State what “bad” looks like in your data model.  
3. Query; note gaps (missing fields = engineering ticket).  
4. Investigate leads with IR discipline if severity warrants.  
5. Convert durable findings into detections or hardening.  
6. Write up what was searched so the next hunt does not restart from zero.

### Baselines and outliers

| Approach | Use |
|----------|-----|
| Peer group (same role hosts) | Spot the odd process parent/child |
| Time baselines | After-hours admin tools |
| Allowlists of approved LOLBins (living-off-the-land binaries) use | Reduce noise carefully |

Baselines drift — revisit after major platform changes.

### When hunting stops and IR starts

Escalate when you have credible evidence of active compromise, data exposure, or attacker control. Preserve evidence before “cleaning” ([9](./9_Digital_Forensics_For_IR.md)). Do not keep “quietly hunting” through an active breach without declaring an incident.

### Authorization

Hunting queries against production logs you are allowed to access is normal SOC (Security Operations Center) work. Replaying attacker techniques to generate telemetry requires lab or written RoE (Rules of Engagement) — same ethics bar as purple tests.

## Apply

1. Write one hunt hypothesis tied to a single ATT&CK technique.  
2. List the exact log fields required; mark missing ones.  
3. Time-box a two-hour hunt; record queries and outcomes.  
4. Convert one finding into a detection ticket or a hardening ticket.

## Further reading

- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE ATT&CK — Data Sources](https://attack.mitre.org/datasources/)
- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [CISA — Cybersecurity best practices](https://www.cisa.gov/cybersecurity-best-practices)
