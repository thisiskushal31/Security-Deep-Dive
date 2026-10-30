# Framework nesting (NIST, ISO, ATT&CK)

[← GRC](./README.md)

> **Progression:** concept → advanced → apply.

How common frameworks nest so you do not treat every document as “the compliance checklist.” Literacy for engineers and security program owners — **not** legal advice, not a certification dump.

Risk *process* basics: [Foundations/5](../Foundations/5_Risk_Management_Process_And_RMF.md). Policy and roles: [Foundations/4](../Foundations/4_Security_Policy_Governance_And_Roles.md). Adversary technique literacy: [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md).

## Concept

Organizations drown when they run five frameworks as five separate programs. Nest them by **job**:

| Layer | Job | Examples |
|-------|-----|----------|
| **Law / contract** | What you must do externally | Privacy regs, customer DPAs, sector rules |
| **Management system** | How the org *runs* security as a system | ISO/IEC 27001 ISMS |
| **Control frameworks** | Catalogs of controls to implement | NIST SP 800-53, CIS Controls |
| **Cybersecurity outcomes** | Shared language for “what good looks like” | [NIST CSF 2.0](https://www.nist.gov/cyberframework) (Govern→Recover) |
| **System authorization path** | How a system is categorized, controlled, assessed, authorized, monitored | NIST RMF ([3](./3_NIST_RMF_Lifecycle.md)) |
| **Workforce** | Roles and skills | [NICE Framework](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center) |
| **Threat techniques** | How adversaries behave | [MITRE ATT&CK](https://attack.mitre.org/) · AI: [MITRE ATLAS](https://atlas.mitre.org/) |
| **Assurance reports** | Evidence for customers / board | SOC 2 Type I/II (Trust Services Criteria) |

ATT&CK sits **beside** controls: it describes adversary behavior those controls should blunt. It is not a substitute for ISO or SOC 2.

**Rule of thumb:** pick one **customer-facing** assurance story (often SOC 2 or ISO 27001). Map engineering controls *up* into that story. Assign a **mapping owner** — do not run five overlapping control catalogs with five owners and no crosswalk.

**Mappings are not equivalency.** A CSF↔800-53 or ISO↔CIS crosswalk shows related language; it does **not** prove you “are compliant” with the other document. Use official informative references / CPRT when you need a published crosswalk ([2](./2_NIST_CSF_2_Outcomes.md)).

```text
Law / contract
    → management system (how we govern)
        → outcomes language (CSF) + control catalogs (800-53 / CIS)
            → implement & evidence (engineering + ops)
                → assurance report (SOC 2 / ISO cert) for outsiders
```

## Advanced

### Which layer answers which question

| Question | Reach for |
|----------|-----------|
| “Are we allowed / required to…?” | Law / contract |
| “Do we have a managed security system?” | ISO 27001-style ISMS ([5](./5_ISO_27001_ISMS_Literacy.md)) |
| “What outcomes should we track?” | CSF 2.0 ([2](./2_NIST_CSF_2_Outcomes.md)) |
| “Which control text do we implement?” | SP 800-53 / CIS ([4](./4_Control_Catalogs_800-53_And_CIS.md)) |
| “Is *this system* authorized to operate?” | RMF ([3](./3_NIST_RMF_Lifecycle.md)) |
| “What evidence will an auditor or customer accept?” | SOC 2 / ISMS audit artifacts ([6](./6_SOC2_And_Evidence.md)) |
| “Who needs which skills?” | NICE ([7](./7_NICE_Workforce_Literacy.md)) |
| “What did the adversary do?” | ATT&CK / ATLAS |

### ATT&CK in the program

| Use | Practice |
|-----|----------|
| Detection engineering | Map alerts to techniques; find coverage gaps |
| Purple / red exercises | Agree on technique IDs in scope |
| IR retrospectives | Tag what happened in ATT&CK language |

ATT&CK is **not** a compliance framework. It does not replace ISO/NIST controls; it describes adversary behavior those controls should address.

**ATLAS** is the parallel living matrix for adversary tactics involving AI. Literacy: [Threats/8](../Threats/8_AI_Era_Threats.md). Use ATT&CK for host/network TTPs and ATLAS when the campaign centers on AI abuse or AI-system compromise.

### AI risk management (governance pointer)

For generative-AI **risk management language** (board/governance), pair CSF/RMF thinking with [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) and the [NIST AI 600-1 Generative AI Profile](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence). Threat classes for AI: [Threats/8](../Threats/8_AI_Era_Threats.md) — do not duplicate them here.

### Adjacent frameworks (pointers only)

| Need | Reach for | Stay thin here because |
|------|-----------|------------------------|
| Privacy *outcomes* (not just security) | [NIST Privacy Framework](https://www.nist.gov/privacy-framework) · foundation privacy: [Foundations/17](../Foundations/17_Privacy_Literacy.md) | Privacy risk ≠ only confidentiality controls |
| Selling cloud to the US government | [FedRAMP](https://www.fedramp.gov/) (800-53/RMF-shaped authorization for CSPs) | Full FedRAMP program ops is its own path |
| Critical-infrastructure priority subset | [CISA CPGs 2.0](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals) | Prioritized practices ≠ full CSF Core |

### Assurance grain (preview)

| Idea | Meaning for builders |
|------|----------------------|
| Control exists | Written **and** implemented (not a slide) |
| Evidence | Logs, tickets, pipeline artifacts, access reviews |
| Scope | Systems and data in the report boundary |
| Continuous | Type II / surveillance audits need ongoing operation |

Depth: [6](./6_SOC2_And_Evidence.md) · delivery gates: [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).

### Related homes (do not rewrite here)

| Slice | Home |
|-------|------|
| Wire / TLS | Networks-Deep-Dive `Security/` |
| Pipeline gates | DevOps-Handbook `Security/` |
| Design trade-offs | System-Design `Security-Tradeoffs/` |
| Risk process intro | [Foundations/5](../Foundations/5_Risk_Management_Process_And_RMF.md) |

## Apply

Implementation habits for a program that already ships software:

1. Name the **customer-facing** assurance target (SOC 2, ISO 27001, or both).  
2. List the **control catalog** you implement day to day (800-53, CIS, cloud baselines…).  
3. Say which **CSF Function** each major control family supports ([2](./2_NIST_CSF_2_Outcomes.md)).  
4. Confirm ATT&CK is used for **detections/exercises**, not as a substitute audit checklist.  
5. Assign a single owner for framework **mappings**.

### Self-check

- [ ] Recite the nesting layers from law → assurance  
- [ ] Explain why ATT&CK is not a compliance framework  
- [ ] State when to open CSF vs 800-53 vs SOC 2  
- [ ] Name one AI governance pointer (AI RMF) without listing AML attacks  

## Next

- [NIST CSF 2.0 outcomes](./2_NIST_CSF_2_Outcomes.md)

## Further reading

- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST Privacy Framework](https://www.nist.gov/privacy-framework)
- [ISO/IEC 27001 overview (ISO)](https://www.iso.org/standard/27001)
- [FedRAMP](https://www.fedramp.gov/)
- [CISA — Cross-Sector Cybersecurity Performance Goals](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals)
