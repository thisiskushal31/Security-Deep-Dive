# NIST RMF lifecycle

[← GRC](./README.md)

> **Progression:** concept → advanced → apply.

How a **system** (or common-control set) is prepared, categorized, controlled, assessed, authorized, and monitored. This note is **program depth** on the Risk Management Framework (RMF). The Foundations intro stays in [Foundations/5](../Foundations/5_Risk_Management_Process_And_RMF.md) — do not treat this as a second CIA/risk course.

Outcomes language (CSF): [2](./2_NIST_CSF_2_Outcomes.md). Control catalogs: [4](./4_Control_Catalogs_800-53_And_CIS.md). Assurance evidence: [6](./6_SOC2_And_Evidence.md).

## Concept

NIST SP 800-37 Rev. 2 defines **seven** RMF steps: one preparatory step plus six main steps. All seven matter.

| Step | Purpose (one line) |
|------|--------------------|
| **Prepare** | Establish context and priorities so org- and system-level RMF can run |
| **Categorize** | Categorize the system and its information based on impact of loss |
| **Select** | Select and tailor controls to reduce risk to an acceptable level |
| **Implement** | Deploy controls and document how they are employed |
| **Assess** | Determine if controls are correct, operating as intended, and effective |
| **Authorize** | A senior official decides residual risk is acceptable (ATO / denial / interim) |
| **Monitor** | Ongoing assessment of controls, changes, risk, and posture reporting |

![NIST Risk Management Framework](../Assets/GRC/NIST_RMF.png)

*Image: [NIST Risk Management Framework](https://csrc.nist.gov/projects/risk-management/about-rmf).*

After Prepare, later steps **need not be strictly sequential**. First-time systems usually go in order; agile delivery, major change, or new risk often loop back.

```text
Prepare
  → Categorize → Select → Implement → Assess → Authorize
                                                      ↓
                                                   Monitor ⟲
                              (events may force revisiting earlier steps)
```

**CSF vs RMF:** CSF is **outcomes language** for the enterprise cybersecurity program. RMF is the **system life-cycle path** to select/implement/assess/authorize/monitor controls. Many orgs use both: CSF for board-facing posture; RMF (or a commercial cousin) for system authorization packages.

## Advanced

### Prepare (org + system)

Prepare is not paperwork theater — it is the scaffold:

| Focus | Examples of what gets set |
|-------|---------------------------|
| Roles | Authorizing official, system owner, control assessors, common-control providers |
| Strategy | Org risk management strategy; privacy and supply-chain risk expectations |
| Inventory | Systems, common controls, inheritance relationships |
| Priorities | What must be authorized first; risk tolerance |

Without Prepare, Categorize/Select become inconsistent between teams.

### Categorize

Impact of loss for **confidentiality, integrity, and availability** of the information types the system processes, stores, or transmits. Typical federal pattern (FIPS 199 / FIPS 200 literacy): rate each CIA objective low / moderate / high, then take the **high-water mark** across those values as the system impact level.

| System impact level | Rule of thumb |
|---------------------|---------------|
| Low | All three CIA values are low |
| Moderate | At least one is moderate; none is high |
| High | At least one is high |

That impact level drives which **SP 800-53B** security baseline you start from ([4](./4_Control_Catalogs_800-53_And_CIS.md)). Higher impact → stronger baseline expectations. Privacy considerations are handled alongside security in modern RMF (including a separate privacy baseline concept in 800-53B).

Write a clear **system description**: boundary, data types, users, connections, environment of operation. Bad boundaries produce useless authorization packages.

### Select

| Idea | Meaning |
|------|---------|
| Baseline | Starting control set for the categorization ([SP 800-53B](https://csrc.nist.gov/pubs/sp/800/53/b/upd1/final) low / moderate / high; plus privacy baseline as applicable) |
| Overlay | Community/tech customization of a baseline (sector, cloud, classified, OT…) |
| Tailoring | Add, remove, or adjust controls based on risk, technology, and environment |
| Allocation | System-specific vs common (inherited) vs hybrid controls |
| Plans | Document intended implementations in security/privacy plans |
| Monitoring strategy | System-level continuous monitoring plan aligned with org strategy |

Selecting *everything* in 800-53 without tailoring is not maturity — it is unreadability. Selecting nothing because “we’re agile” is not risk acceptance — it is unowned risk.

**FedRAMP** (cloud services for US federal use) is the widely known commercial/government cousin: still 800-53/RMF-shaped authorization, with FedRAMP-published baselines rather than inventing your own. Pointer only: [fedramp.gov](https://www.fedramp.gov/).

### Implement → Assess

Implement controls **as planned**, then update plans with **as-built** detail (config, owners, evidence locations).

Assess answers three questions: implemented correctly? operating as intended? producing desired outcomes? Outputs typically include assessment reports, initial remediation, and a **plan of action and milestones (POA&M)** for residual findings.

### Authorize

A senior **authorizing official** accepts (or rejects) residual risk for operating the system or providing common controls. The authorization package aggregates plans, assessment results, POA&Ms, and risk determination. Authorization is a **decision**, not a certificate that risk is zero.

### Monitor (continuous)

Monitor keeps authorization from going stale:

- Reassess control effectiveness on a defined cadence / trigger  
- Document system and environment changes  
- Re-run risk / impact analysis when needed  
- Report security and privacy posture  
- Feed significant changes back into Select / Implement / Assess / Authorize  

Commercial programs that never say “RMF” still need the same loop: change → evidence → risk call → ongoing visibility. Continuous monitoring program design depth lives in [NIST SP 800-137](https://csrc.nist.gov/pubs/sp/800/137/final) (org-level strategy that system Monitor plans plug into).

### Common controls and inheritance

| Pattern | Meaning for builders |
|---------|----------------------|
| Common control | Provided once (for example org IdP, logging platform) and inherited by many systems |
| System-specific | Implemented inside the system boundary |
| Hybrid | Shared responsibility (cloud shared-responsibility is the everyday cousin) |

Name your inheritance explicitly. “We thought the cloud provider covered that” is a classic authorization-package failure mode.

## Apply

Implementation habits for a real system (or service) boundary:

1. Draw your system boundary on one page (data types, users, key connections).  
2. Assign a provisional CIA impact (L/M/H) using high-water mark and say *why*.  
3. List five controls you already run and mark each common / system-specific / hybrid.  
4. Name who would be the real-world “authorizing official” for a go-live risk call.  
5. Point to one continuous-monitoring signal (vuln scan cadence, config drift, access review).  
6. Cross-check Foundations vocabulary in [Foundations/5](../Foundations/5_Risk_Management_Process_And_RMF.md) if any term feels fuzzy.

### Self-check

- [ ] Recite the seven RMF steps  
- [ ] Explain why Monitor can force revisiting Select or Authorize  
- [ ] Contrast CSF outcomes with RMF authorization in one sentence  
- [ ] Define common vs system-specific control  
- [ ] State what an authorizing official actually decides  
- [ ] Explain high-water mark for system impact in one sentence  

## Next

- [Control catalogs (800-53 and CIS)](./4_Control_Catalogs_800-53_And_CIS.md)

## Further reading

- [NIST Risk Management Framework (project)](https://csrc.nist.gov/projects/risk-management)
- [NIST SP 800-37 Rev. 2](https://csrc.nist.gov/pubs/sp/800/37/r2/final)
- [NIST SP 800-53 Rev. 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final)
- [NIST SP 800-53B — Control Baselines](https://csrc.nist.gov/pubs/sp/800/53/b/upd1/final)
- [NIST SP 800-30 Rev. 1 — Guide for Conducting Risk Assessments](https://csrc.nist.gov/pubs/sp/800/30/r1/final)
- [NIST SP 800-39 — Managing Information Security Risk](https://csrc.nist.gov/pubs/sp/800/39/final)
- [NIST SP 800-137 — Information Security Continuous Monitoring](https://csrc.nist.gov/pubs/sp/800/137/final)
- [FedRAMP](https://www.fedramp.gov/)
