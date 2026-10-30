# NIST CSF 2.0 outcomes

[← GRC](./README.md)

> **Progression:** concept → advanced → apply.

Shared **outcomes language** for cybersecurity risk — so executives, managers, and engineers can talk about the same program without a single control catalog. Nesting map: [1](./1_NIST_ISO_And_ATTCK.md). Controls you implement: [4](./4_Control_Catalogs_800-53_And_CIS.md). Detection/IR homes: [Defensive-Ops](../Defensive-Ops/README.md).

Not a checklist of products. The CSF does **not** prescribe *how* to achieve outcomes; it names *what* good cybersecurity risk management looks like at a high level.

## Concept

The **CSF Core** is a taxonomy of cybersecurity outcomes. Outcomes nest from high-level verbs down to specific results: **Function → Category → Subcategory**. They are not a product checklist — *how* you achieve an outcome varies by org, risk, and technology.

![CSF Core structure](../Assets/GRC/NIST_CSF_2_Core_Structure.png)

*Image: CSF Core structure (Function → Category → Subcategory) — [NIST CSWP 29, CSF 2.0](https://csrc.nist.gov/pubs/cswp/29/the-nist-cybersecurity-framework-csf-20/final).*

**CSF 2.0** organizes the Core into six Functions:

| Function | ID | Meaning (one line) |
|----------|-----|--------------------|
| **Govern** | GV | Strategy, expectations, and policy are established, communicated, and monitored |
| **Identify** | ID | Current cybersecurity risks (and related assets/suppliers) are understood |
| **Protect** | PR | Safeguards manage risk to assets (identity, data, platforms, resilience…) |
| **Detect** | DE | Possible attacks and compromises are found and analyzed |
| **Respond** | RS | Actions are taken on detected incidents (contain, analyze, communicate…) |
| **Recover** | RC | Affected assets and operations are restored; recovery is communicated |

![CSF 2.0 Functions](../Assets/GRC/NIST_CSF_2_Functions.png)

*Image: CSF Functions wheel (Govern at the center) — [NIST CSWP 29, CSF 2.0](https://csrc.nist.gov/pubs/cswp/29/the-nist-cybersecurity-framework-csf-20/final).*

Functions are **concurrent**. Govern, Identify, Protect, and Detect should run continuously. Respond and Recover stay ready and run when incidents happen.

**Govern** is a top-level Function in 2.0 (CSF 1.1 had five Functions). It sits at the center: organizational context, risk strategy, roles/authorities, policy, oversight, and cybersecurity supply-chain risk management.

## Advanced

### Categories (awareness map)

You do not need to memorize every Subcategory. Know the Category map so conversations stay precise:

| Function | Categories (identifiers) |
|----------|--------------------------|
| **Govern** | Organizational Context (GV.OC); Risk Management Strategy (GV.RM); Roles, Responsibilities, and Authorities (GV.RR); Policy (GV.PO); Oversight (GV.OV); Cybersecurity Supply Chain Risk Management (GV.SC) |
| **Identify** | Asset Management (ID.AM); Risk Assessment (ID.RA); Improvement (ID.IM) |
| **Protect** | Identity Management, Authentication, and Access Control (PR.AA); Awareness and Training (PR.AT); Data Security (PR.DS); Platform Security (PR.PS); Technology Infrastructure Resilience (PR.IR) |
| **Detect** | Continuous Monitoring (DE.CM); Adverse Event Analysis (DE.AE) |
| **Respond** | Incident Management (RS.MA); Incident Analysis (RS.AN); Incident Response Reporting and Communication (RS.CO); Incident Mitigation (RS.MI) |
| **Recover** | Incident Recovery Plan Execution (RC.RP); Incident Recovery Communication (RC.CO) |

### Profiles

A **CSF Organizational Profile** describes current and/or target posture **in terms of Core outcomes**.

| Profile type | Meaning |
|--------------|---------|
| **Current Profile** | Outcomes the organization is achieving now (and how well) |
| **Target Profile** | Desired outcomes, prioritized for risk and mission |
| **Community Profile** | Published baseline for a sector, technology, or shared use case — often a starting point for a Target Profile |

Profiles are a communication and prioritization tool, not a second audit standard. One practical loop: scope → gather inputs → create Current/Target → analyze gaps → implement and update → repeat.

![CSF Organizational Profile steps](../Assets/GRC/NIST_CSF_2_Organizational_Profile.png)

*Image: Steps for creating and using a CSF Organizational Profile — [NIST CSWP 29, CSF 2.0](https://csrc.nist.gov/pubs/cswp/29/the-nist-cybersecurity-framework-csf-20/final).*

### Tiers

**Tiers** characterize the *rigor* of cybersecurity risk governance and management practices (context for how the org views and manages risk). Progression:

| Tier | Label | Idea |
|------|-------|------|
| 1 | Partial | Informal / ad hoc |
| 2 | Risk Informed | Risk considered, but not fully institutionalized |
| 3 | Repeatable | Practices are defined and followed as a program |
| 4 | Adaptive | Agile, risk-informed, continuously improving |

Tiers set tone for Profiles. Moving up a Tier is not a trophy — it should match mission risk and capacity.

### Informative references (how CSF connects to catalogs)

CSF outcomes stay technology-neutral. **Informative references** (crosswalks) point from a Subcategory to related language in other documents (SP 800-53, CIS Controls, ISO/IEC 27001, and many community mappings).

| Tool | Use |
|------|-----|
| [CSF 2.0 Reference Tool](https://csrc.nist.gov/Projects/cybersecurity-framework/Filters#/csf/filters) | Filter Core + selected informative references; export |
| [CSF 2.0 Informative References page](https://www.nist.gov/cyberframework/informative-references) | Downloads and recent mappings (including CIS 8.1, 800-53, ISO 27001) |
| [CPRT](https://csrc.nist.gov/Projects/cprt) | Browse/export NIST cybersecurity & privacy reference data |
| [SP 1347 QSG](https://csrc.nist.gov/pubs/sp/1347/final) | How to use informative references without drowning |

A published mapping is a **navigation aid**. Implementing the mapped 800-53 control does not automatically “complete” the CSF outcome (scope, rigor, and evidence still matter).

### How engineers use CSF without drowning

| Do | Don’t |
|----|-------|
| Tag major controls and epics to a Function/Category | Treat Subcategories as a mandatory ticket for every PR |
| Use Current vs Target language in roadmap talks | Duplicate the entire Core into a spreadsheet nobody reads |
| Point Detect/Respond gaps to SIEM/IR work | Claim “we’re CSF compliant” without a Profile or evidence story |
| Let Govern drive priorities (including suppliers) | Buy tools Function-by-Function with no overlap plan |

[CISA Cross-Sector Cybersecurity Performance Goals (CPGs) 2.0](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals) are a related **prioritized subset** for critical infrastructure (aligned to CSF 2.0 Functions, including Govern). Use CPGs to sequence first wins; use CSF Profiles for the fuller outcomes conversation. Mapping a CPG to a CSF Category is **not** the same as satisfying that Category end-to-end. Sector-Specific Goals (SSGs) exist for some sectors on the same CISA page.

## Apply

Implementation habits (not a second Profile template):

1. For your product or team, write one Current outcome and one Target outcome under **Govern** and under **Protect**.  
2. Name which Category each sits in (use the table above).  
3. List the engineering evidence that would show the Target (config, test, ticket, metric).  
4. If Detect is weak, open [Defensive-Ops](../Defensive-Ops/README.md) before buying another “AI SOC” box.  
5. Move to RMF when you need system authorization / control selection depth ([3](./3_NIST_RMF_Lifecycle.md)).

### Self-check

- [ ] Name all six CSF 2.0 Functions in order  
- [ ] Explain why Govern sits at the center  
- [ ] Contrast Current vs Target Profile in one sentence each  
- [ ] Name the four Tier labels  
- [ ] Give one engineer-facing use of CSF that is not “compliance theater”  

## Next

- [NIST RMF lifecycle](./3_NIST_RMF_Lifecycle.md)

## Further reading

- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [NIST CSWP 29 — CSF 2.0](https://csrc.nist.gov/pubs/cswp/29/the-nist-cybersecurity-framework-csf-20/final)
- [NIST SP 1299 — CSF 2.0 Resource & Overview Guide](https://csrc.nist.gov/pubs/sp/1299/final)
- [CSF 2.0 Reference Tool](https://csrc.nist.gov/Projects/cybersecurity-framework/Filters#/csf/filters)
- [CSF 2.0 Informative References](https://www.nist.gov/cyberframework/informative-references)
- [NIST SP 1347 — Informative References Quick-Start Guide](https://csrc.nist.gov/pubs/sp/1347/final)
- [Cybersecurity and Privacy Reference Tool (CPRT)](https://csrc.nist.gov/Projects/cprt)
- [CISA — Cross-Sector Cybersecurity Performance Goals](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals)
