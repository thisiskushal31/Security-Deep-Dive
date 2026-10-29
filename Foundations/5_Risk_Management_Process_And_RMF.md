# Risk management process and RMF

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Risk vocabulary lives in [1](./1_CIA_And_Risk.md). This note is the **process**: how organizations categorize systems, pick controls, prove they work, authorize operation, and keep watching. Supply-chain depth: [Cloud-Security/3](../Cloud-Security/3_Supply_Chain.md). Continuity patterns in design: System-Design disaster-recovery notes.

## Concept

## Risk management loop

```text
Identify assets and threats
  → Assess likelihood and impact
    → Treat (mitigate / avoid / transfer / accept)
      → Implement controls
        → Assess and monitor
          → Report and improve
```

Risk is not a one-time spreadsheet. Changes to architecture, vendors, or data class reopen assessment ([9](./9_Change_Management_And_Security.md)).

## Advanced

## NIST Risk Management Framework (RMF)

The RMF integrates security, privacy, and cyber supply-chain risk activities into the system life cycle. Steps:

| Step | What happens |
|------|----------------|
| **Prepare** | Organize so the enterprise can manage security and privacy risk |
| **Categorize** | Categorize the system and the information it processes, stores, and transmits based on impact |
| **Select** | Select a control set (for example NIST SP 800-53) based on risk |
| **Implement** | Deploy controls and document how they are realized |
| **Assess** | Determine whether controls are in place, operating as intended, and effective |
| **Authorize** | A senior official makes a risk-based decision to authorize the system to operate |
| **Monitor** | Continuously monitor control implementation and risk |

![NIST Risk Management Framework](../Assets/Foundations/NIST_RMF.png)

*Image: [NIST Risk Management Framework](https://csrc.nist.gov/projects/risk-management/about-rmf).*

Impact categorization commonly uses low/moderate/high impact for confidentiality, integrity, and availability of the information types involved. Higher impact → stronger baseline expectations.

## Business impact and continuity (literacy)

| Concept | Meaning |
|---------|---------|
| Business impact analysis (BIA) | Which processes and systems matter; what downtime or data loss costs |
| Recovery time objective (RTO) | How quickly a process must return |
| Recovery point objective (RPO) | How much data loss (time) is tolerable |
| Business continuity | Keep critical functions running through disruption |
| Disaster recovery | Restore systems and data after a major failure |

Security availability controls and backup/restore drills support these objectives. Foundation expansion: [21](./21_Resilience_Continuity_And_Recovery_Literacy.md). Detailed DR patterns stay in System-Design.

## Supply-chain risk (foundation)

Acquiring software, hardware, cloud services, and libraries imports someone else’s risk: tampering, counterfeits, vulnerable components, weak upstream identity. Mitigations at foundation level: inventory what you depend on, require minimum security in contracts/services, prefer signed artifacts and SBOMs, monitor vendors. Implementation: [Cloud-Security/3](../Cloud-Security/3_Supply_Chain.md) and Handbook supply-chain gates.

## Assurance

| Idea | Meaning |
|------|---------|
| Assurance | Confidence that controls are correct and effective |
| Assessment | Test or evaluate controls (design and operation) |
| Authorization to operate | Formal acceptance of residual risk for a system in a given environment |
| Continuous monitoring | Ongoing visibility so authorization does not go stale |

## Security awareness as a control

People are part of the control system. Awareness and role-based training reduce successful phishing and misconfiguration. Measure with more than attendance: reporting rates, simulated phishing trends, and whether privileged roles get deeper training. This is a technical program control, not a hiring process.

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] Recite Prepare → Monitor in order  
- [ ] Explain categorize vs select vs authorize  
- [ ] Define RTO and RPO in one line each  
- [ ] Name one supply-chain risk and one mitigation  

## Next

- [Assets, data states, and assurance](./6_Assets_Data_States_And_Assurance.md)

## Further reading

- [NIST Risk Management Framework](https://csrc.nist.gov/projects/risk-management/about-rmf)
- [NIST SP 800-37 Rev. 2 — RMF for Information Systems and Organizations](https://csrc.nist.gov/pubs/sp/800/37/r2/final)
- [NIST SP 800-30 Rev. 1 — Guide for Conducting Risk Assessments](https://csrc.nist.gov/pubs/sp/800/30/r1/final)
- [NIST SP 800-161 Rev. 1 — Cybersecurity Supply Chain Risk Management](https://csrc.nist.gov/pubs/sp/800/161/r1/final)
