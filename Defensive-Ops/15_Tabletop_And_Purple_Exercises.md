# Tabletop and purple exercises

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Exercises prove whether playbooks and detections work **before** production panic. Tabletops stress decisions and comms; purple exercises stress telemetry and detection paths with **authorized** adversary emulation. Metrics loop: [11](./11_Metrics_Purple_And_Improvement.md). Ethics: [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

## Concept

### Two complementary drills

| Type | Primary stress | Typical output |
|------|----------------|----------------|
| **Tabletop** | Decisions, authority, comms, dependencies | Runbook and ownership gaps |
| **Purple / detection validation** | Telemetry, alerts, triage path | Missed detections, tune tickets |

```text
Design scenario ──▶ run (tabletop and/or purple)
                         │
                         ▼
                   observe gaps
                         │
                         ▼
              tickets → re-test → update coverage map
```

### Scenario starter pack (timeless)

| Scenario | Exercises |
|----------|-----------|
| Ransomware on a segment | Containment authority, backups, comms |
| Business email compromise | IdP (Identity Provider) revoke, mail rules, fraud path |
| Cloud access key leak | Key rotate, audit hunt, public storage check |
| Supplier breach notice | Contract clocks, shared IOCs (indicators of compromise) |
| Insider data staging | Privacy / HR / legal coordination ([14](./14_Comms_Notification_And_External_Reporting.md)) |

## Advanced

### Tabletop design habits

| Habit | Why |
|-------|-----|
| Clear objectives (3 max) | “Practice everything” practices nothing |
| Injects on a timeline | Forces decisions under incomplete info |
| Right people in the room | Missing counsel = fake comfort |
| Scribe + after-action | Lessons become tickets |
| No-blame facilitation | Honesty over theater |

### Purple design habits

| Habit | Why |
|-------|-----|
| Written scope + disclosed source IPs / lab targets | Blue can tell test from attack |
| Mapped ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) techniques | Coverage math |
| Success = detect **or** explain miss with telemetry gap | Both outcomes are valuable |
| Stop rules for production safety | Prevent accidental DoS (denial of service) |
| Re-test after fixes | Close the loop |

Offense phase shape: [Offensive](../Offensive/README.md). Recon noise as detection fuel: [Reconnaissance/11](../Reconnaissance/11_Detect_Harden_And_Continuous_Recon.md).

### Cadence

| Cadence | Activity |
|---------|----------|
| Quarterly | Tabletop for one crown-jewel scenario |
| Monthly / per sprint | Small purple for 1–2 techniques |
| After major incidents | Mini-tabletop on the failure mode |
| After platform changes | Re-validate critical detections |

### What “pass” means

A pass is not “everyone felt good.” A pass is: objectives tested, gaps ticketed, owners dated, and at least one re-test scheduled.

## Apply

1. Schedule one 90-minute tabletop with three objectives.  
2. Run one authorized purple for a single ATT&CK technique; record detect yes/no.  
3. File ≤5 tickets; kill the rest of the wishlist.  
4. Re-test the top missed detection within two weeks.

## Further reading

- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [CISA — Incident and Vulnerability Response Playbooks](https://www.cisa.gov/resources-tools/resources/federal-government-cybersecurity-incident-and-vulnerability-response-playbooks)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [NIST CSF 2.0](https://www.nist.gov/cyberframework)
