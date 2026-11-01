# Incident response lifecycle

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Incidents are when detection becomes **coordinated action**. Current NIST (National Institute of Standards and Technology) guidance embeds IR (incident response) across CSF (Cybersecurity Framework) 2.0 risk management — prepare continuously, reduce likelihood and impact, and improve Detect / Respond / Recover outcomes. Industry playbooks still speak in familiar operational phases (prepare → detect/analyze → contain → eradicate/recover → post-incident). Use both: **CSF outcomes for program design**, **phased playbooks for night-of execution**.

## Concept

### Program view (CSF-shaped)

IR is not a binder you open only during a breach. It spans:

| Focus | Examples |
|-------|----------|
| Prepare | Contacts, access, retainers, tabletop, logging readiness |
| Reduce incidents | Hardening, patch urgency, phishing-resistant MFA (multi-factor authentication) |
| Detect & analyze | Triage, scoping, severity |
| Respond & recover | Containment through service restoration |
| Learn | Detections, design, and runbooks change |

```text
Govern / Identify / Protect
            │
            ▼
   Detect ──▶ Respond ──▶ Recover
            │
            └──▶ improve controls & detections ([11](./11_Metrics_Purple_And_Improvement.md))
```

### Operational phases (execution view)

| Phase | Job |
|-------|-----|
| **Preparation** | Playbooks, tooling access, comms trees, evidence kit |
| **Detection & analysis** | Validate, scope systems/identities/data, severity |
| **Containment** | Stop the bleeding without destroying needed evidence |
| **Eradication** | Remove attacker control and root cause |
| **Recovery** | Restore service; heightened monitoring |
| **Post-incident** | Timeline, lessons, tickets that actually close |

Contain/eradicate/recover detail: [8](./8_Contain_Eradicate_Recover.md). Evidence: [9](./9_Digital_Forensics_For_IR.md).

### Severity and ownership

| Ask | Why |
|-----|-----|
| What data class is exposed? | Legal / customer notification path |
| Is the attacker still active? | Containment urgency |
| Which system owner? | You cannot page “security@” forever |
| Regulatory clocks? | Breach notification deadlines |

Document who can take production offline **before** you need them ([1](./1_Defensive_Ops_And_SOC_Shape.md)).

## Advanced

### Playbook discipline

Federal-oriented IR and vulnerability response playbooks standardize coordination, tracking, and checklists. Adapt the *shape* to your org: preparation checklist, detection/analysis steps, containment criteria, eradication/recovery, and post-incident actions — including who to notify.

| Practice | Failure mode |
|----------|--------------|
| Written severity matrix | Every alert feels Sev-1 |
| Comms templates (internal / customer / regulator) | Ad-libbing under stress |
| Evidence before wipe | “Cleaned” hosts with no timeline |
| Parallel vuln response when exploited CVE (Common Vulnerabilities and Exposures) | IR and patch teams colliding — see [Vulnerability-Management](../Vulnerability-Management/README.md) |

### Analysis quality

| Habit | Why |
|-------|-----|
| Timeline of facts vs hypotheses | Stops rumor-driven containment |
| Scope in / scope out lists | Clear “not affected” |
| Preserve volatile data when needed | Memory/creds vanish on reboot |
| Single incident commander | Conflicting orders |

### Major incident / crisis mode

When impact crosses a threshold (customer-facing outage, confirmed data theft, ransomware at scale):

| Shift | Why |
|-------|-----|
| Named incident commander with deputies | One decision voice |
| Separate comms lead | Technical channel stays usable ([14](./14_Comms_Notification_And_External_Reporting.md)) |
| Executive brief cadence | Decisions on downtime / disclosure |
| Evidence lead | Prevents “everyone deletes logs” |
| Liaison to legal / HR / fraud | Parallel tracks without chaos |

### Tabletop before the page

Walk a ransomware, business-email-compromise, and cloud-key-leak scenario. Note decision gaps. Fix access and runbooks while calm. Full exercise design: [15](./15_Tabletop_And_Purple_Exercises.md).

### Preparation checklist (timeless)

| Ready? | Item |
|--------|------|
| □ | Out-of-band contacts (phone tree if IdP/mail down) |
| □ | Break-glass admin path documented and monitored |
| □ | Log / EDR (Endpoint Detection and Response) access for responders |
| □ | Evidence kit / snapshot rights |
| □ | Retainer / DFIR (digital forensics and incident response) reach-out path |
| □ | Severity matrix + containment authority |

## Apply

1. Write a one-page severity matrix for your environment.  
2. Name incident commander + deputies for business hours and after hours.  
3. Run a 60-minute tabletop; file three concrete tickets from gaps.  
4. Confirm legal / exec notification contacts are current.  
5. Test that responders can reach critical logs when SSO (single sign-on) is degraded.

## Further reading

- [NIST SP 800-61 Rev. 3 — Incident Response Recommendations (CSF 2.0 Community Profile)](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [NIST CSF 2.0](https://www.nist.gov/cyberframework)
- [CISA — Federal Government Cybersecurity Incident and Vulnerability Response Playbooks](https://www.cisa.gov/resources-tools/resources/federal-government-cybersecurity-incident-and-vulnerability-response-playbooks)
- [NIST — Incident Response project](https://csrc.nist.gov/projects/incident-response)
