# NICE workforce literacy

[← GRC](./README.md)

> **Progression:** concept → advanced → apply.

Shared language for **cybersecurity work** — categories, work roles, and building blocks for skills — so hiring, training, and team design are not inventing titles from scratch. Nesting map: [1](./1_NIST_ISO_And_ATTCK.md). CSF outcomes those people deliver: [2](./2_NIST_CSF_2_Outcomes.md).

## Concept

The **NICE Workforce Framework for Cybersecurity** (NICE Framework) is NIST’s nationally focused lexicon for cybersecurity work and workers, used across public, private, and academic sectors. Explore interactively on [NICCS](https://niccs.cisa.gov/workforce-development/nice-framework); downloads and structure live in the [NICE Framework Resource Center](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center).

Core components:

| Component | Meaning |
|-----------|---------|
| **Work Role Categories** | High-level groupings of common cybersecurity functions |
| **Work Roles** | Groupings of work someone is responsible/accountable for — **not** the same as job titles or occupations |
| **Task / Knowledge / Skill (TKS)** | Discrete building blocks: what is done and what is required to do it |
| **Competency Areas** | Clusters of related Knowledge and Skill statements for a domain |

One person often covers **multiple** work roles. One job title often mixes roles from different categories. Use NICE to describe *work*, not to force a 1:1 org chart.

```text
Category  →  Work Role  →  Tasks
                              ↑
                     Knowledge + Skills
```

## Advanced

### Work Role Categories (current NICCS / Components view)

Category names evolve; confirm on NICCS when staffing. Literacy snapshot aligned with **NICE Framework Components v2.2.0** as presented on NICCS:

| Category | ID | Focus |
|----------|-----|--------|
| **Oversight and Governance** | OG | Leadership, management, direction, advocacy so the org manages cyber risk and can conduct cyber work (policy, authorization, SCRM, workforce mgmt, assessment…) |
| **Design and Development** | DD | Research, conceptualize, design, develop, and test secure technology systems |
| **Implementation and Operation** | IO | Implement, administer, configure, operate, and maintain systems securely and efficiently |
| **Protection and Defense** | PD | Protect against, identify, and analyze risks; investigate cyber events/crimes related to systems and networks (includes defensive ops, IR, vuln analysis, forensics…) |
| **Investigation** | IN | Investigation-focused roles (for example cybercrime investigation, digital evidence analysis) as listed on NICCS |

**Note:** Some specialized categories historically associated with NICE (for example Cyberspace Effects / Cyberspace Intelligence) are directed to the **DoD Cyber Workforce Framework (DCWF)** in current NICCS guidance. Prefer NICCS/NIST current pages over memorized older seven-category charts from training decks.

### Example roles engineers meet often

| Category | Example work roles |
|----------|----------------------------------------|
| OG | Cybersecurity Policy and Planning; Systems Authorization; Security Control Assessment; Executive Cybersecurity Leadership |
| DD | Secure Software Development; Cybersecurity Architecture; Software Security Assessment |
| IO | Systems Administration; Network Operations; Systems Security Analysis |
| PD | Incident Response; Vulnerability Analysis; Defensive Cybersecurity; Threat Analysis; Digital Forensics |
| IN | Cybercrime Investigation; Digital Evidence Analysis |

Exact role lists change with Framework Component versions — treat the table as orientation, then search NICCS for the live definition.

### How to use NICE without HR theater

| Do | Don’t |
|----|-------|
| Tag open roles with 1–3 NICE work roles | Rename every engineer “Cyber Ninja” with no TKS |
| Build learning paths from Competency Areas / TKS gaps | Assume one NICE role = one headcount forever |
| Align OG roles with CSF Govern / RMF Authorize owners | Duplicate the entire Framework into an internal wiki |
| Use PD/IO language when scoping on-call and IR staffing | Treat NICE as a compliance control catalog |

### Tie-back to this deep dive

| Need | GRC / Security-Deep-Dive home |
|------|-------------------------------|
| Program outcomes | [CSF 2.0](./2_NIST_CSF_2_Outcomes.md) |
| Authorization / assessor mindset | [RMF](./3_NIST_RMF_Lifecycle.md) · OG roles on NICCS |
| Build secure software | [AppSec](../AppSec/README.md) · DD roles |
| Detect / respond | [Defensive-Ops](../Defensive-Ops/README.md) · PD roles |
| Adversary literacy | [Threats](../Threats/README.md) · [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md) |

## Apply

Implementation habits for staffing and learning paths:

1. Pick your current job title and map it to **two** NICE work roles (primary + secondary).  
2. List three Tasks you actually perform weekly; check whether they sit under those roles on NICCS.  
3. Name one Competency Area or skill gap you would train next (detection, secure design, authorization…).  
4. For a hiring doc, replace vague “3 years security” with one NICE work role + two must-have skills.  
5. If you own governance, confirm an OG-shaped owner exists for policy and for system authorization decisions.

### Self-check

- [ ] Explain why a work role is not a job title  
- [ ] Name the five Work Role Categories in the current NICCS snapshot  
- [ ] Give one OG and one PD example role  
- [ ] State where to verify the latest Component version (NICCS / NIST Resource Center)  

## Further reading

- [NICCS — NICE Framework](https://niccs.cisa.gov/workforce-development/nice-framework)
- [NIST — NICE Framework Resource Center](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center)
- [NIST — NICE Framework current versions](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center/nice-framework-current-versions)
- [NICCS — Work Role search](https://niccs.cisa.gov/tools/nice-framework/work-role)
