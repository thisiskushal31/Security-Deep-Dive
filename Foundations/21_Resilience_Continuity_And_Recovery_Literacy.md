# Resilience, continuity, and recovery literacy

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Availability is a CIA objective ([1](./1_CIA_And_Risk.md)). Resilience is how you keep (or restore) critical functions when something fails — attack, outage, or operator error. BIA/RTO/RPO first appeared in [5](./5_Risk_Management_Process_And_RMF.md); this note expands the foundation set Security+ architecture/operations expect.

Design patterns: System-Design DR notes. IR process: [Defensive-Ops/2](../Defensive-Ops/2_Incident_Response.md).

## Concept

## Continuity vocabulary

| Term | Meaning |
|------|---------|
| Business impact analysis (BIA) | Which processes matter; impact of downtime and data loss |
| RTO | Maximum acceptable time to restore a process |
| RPO | Maximum acceptable data-loss window |
| Business continuity (BC) | Keep critical business functions running through disruption |
| Disaster recovery (DR) | Restore IT systems and data after major failure |
| High availability (HA) | Design to avoid downtime (redundancy, failover) |
| COOP | Continuity of operations — often used in public-sector framing |

```text
BIA → set RTO/RPO → design HA/DR/backups → test → improve
```

## Advanced

## Resilience building blocks

| Block | Role |
|-------|------|
| Redundancy | Multiple instances / sites so one failure is not total loss |
| Backups | Recover data; prefer offline/immutable copies against ransomware |
| Restore drills | Prove backups work — untested backups are hope |
| Diversity | Avoid single vendor/version monoculture where practical |
| Capacity / rate limits | Survive load and some DoS classes |
| Degrade gracefully | Critical paths survive when noncritical features die |
| Incident + recovery playbooks | Who does what when availability burns |

## Security events vs disasters

| Scenario | Primary track |
|----------|---------------|
| Ransomware / destructive attack | IR + recover from clean backups / rebuild |
| Regional cloud outage | DR / multi-site continuity |
| Bad deploy | Change rollback ([9](./9_Change_Management_And_Security.md)) |
| Facility issue | Physical + BC plans ([2](./2_Controls_And_Physical.md)) |

Availability controls without tested recovery are incomplete Protect/Recover coverage ([2](./2_Controls_And_Physical.md) CSF map).

## Apply
1. Name critical services and their RTO/RPO.  
2. Encrypt and protect backups like production data ([6](./6_Assets_Data_States_And_Assurance.md)).  
3. Test restore on a schedule; record results.  
4. Separate backup credentials from production admin identity.  
5. After incidents, fix detection **and** recovery gaps.

## Self-check

- [ ] Define RTO vs RPO with one example each  
- [ ] Explain why immutable/offline backups matter for ransomware  
- [ ] List three resilience blocks beyond “add another server”  

## Next

Foundations complete → [Threats](../Threats/README.md)

Practice depth stubs when ready: [Threat-Modeling](../Threat-Modeling/README.md) · [Threat-Intelligence](../Threat-Intelligence/README.md) · [Vulnerability-Management](../Vulnerability-Management/README.md)

## Further reading

- [NIST SP 800-34 Rev. 1 — Contingency Planning Guide](https://csrc.nist.gov/pubs/sp/800/34/r1/final)
- [NIST Cybersecurity Framework — Recover](https://www.nist.gov/cyberframework)
- [CISA — Cybersecurity Incident & Vulnerability Response Playbooks](https://www.cisa.gov/resources-tools/resources/cybersecurity-incident-vulnerability-response-playbooks)
