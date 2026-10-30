# Control catalogs (800-53 and CIS)

[← GRC](./README.md)

> **Progression:** concept → advanced → apply.

Where **control text** comes from when CSF tells you the outcome and RMF tells you the process. Nesting: [1](./1_NIST_ISO_And_ATTCK.md). RMF Select step: [3](./3_NIST_RMF_Lifecycle.md). This is a **family / role map**, not an encyclopedia of every control statement.

## Concept

Two catalogs show up constantly in engineering and GRC conversations:

| Catalog | Role |
|---------|------|
| **NIST SP 800-53** | Broad, modular **security and privacy control** catalog (families, base controls, enhancements). Common in federal / FedRAMP-shaped programs and as a reference lexicon elsewhere. |
| **CIS Critical Security Controls (CIS Controls)** | **Prioritized, prescriptive** safeguards aimed at defending against prevalent attacks. Useful as an implementation baseline and roadmap — especially with Implementation Groups. |

**Do not** paste both catalogs into the ticket system. Pick a primary day-to-day baseline (often CIS IG1/IG2 or a cloud baseline) and keep a **mapping** to 800-53 / CSF / customer questionnaires.

Current CIS release to cite in conversation: **CIS Controls v8.1** (aligns mappings to CSF 2.0, including Govern). Always confirm on the official CIS Controls pages when you need the latest download.

```text
CSF outcome (“Protect identity…”)
    → choose catalog language (800-53 IA/AC… or CIS Account/Access…)
        → implement in product / platform
            → evidence for RMF Assess or SOC 2 / ISMS audit
```

## Advanced

### SP 800-53 Rev. 5 — twenty families

Controls are grouped into families with two-letter IDs. Base controls can have **enhancements** that add strength or specificity for higher-impact systems.

| ID | Family | ID | Family |
|----|--------|----|--------|
| AC | Access Control | PE | Physical and Environmental Protection |
| AT | Awareness and Training | PL | Planning |
| AU | Audit and Accountability | PM | Program Management |
| CA | Assessment, Authorization, and Monitoring | PS | Personnel Security |
| CM | Configuration Management | PT | PII Processing and Transparency |
| CP | Contingency Planning | RA | Risk Assessment |
| IA | Identification and Authentication | SA | System and Services Acquisition |
| IR | Incident Response | SC | System and Communications Protection |
| MA | Maintenance | SI | System and Information Integrity |
| MP | Media Protection | SR | Supply Chain Risk Management |

**Engineer-facing clusters** (not official groupings — for navigation):

| Cluster | Families you will touch often |
|---------|-------------------------------|
| Identity & access | AC, IA, PS (people side) |
| Build & change | CM, SA, SI |
| Observe & respond | AU, IR, CA (assessment/monitoring) |
| Resilience | CP, SC (comms protection), PE |
| Program / privacy / supply chain | PL, PM, RA, PT, SR, AT |

### Baselines, overlays, assessment, machine-readable catalogs

| Artifact | Job |
|----------|-----|
| **SP 800-53** | Full control *catalog* (families, base controls, enhancements) |
| **SP 800-53B** | Starting **baselines**: security Low / Moderate / High (from impact) plus a **Privacy** baseline applied irrespective of impact level |
| **Overlays** | Further customization for a community, technology, or environment (on top of a baseline) |
| **SP 800-53A** | **Assessment procedures** — how assessors check whether controls work |
| **OSCAL** | Machine-readable catalogs/profiles (XML/JSON/YAML) used by tooling and by programs such as FedRAMP |

Baselines are a **starting point**, not the finish line. Tailoring is expected ([3](./3_NIST_RMF_Lifecycle.md)). Not every control in 800-53 appears in every baseline.

**FedRAMP** publishes its own Low/Moderate/High profiles derived from 800-53 for cloud service authorizations — use the FedRAMP baseline when that is the customer, do not silently substitute a homemade subset.

CSF↔800-53 and CIS↔CSF informative references exist so you can **trace** without pretending catalogs are identical ([2](./2_NIST_CSF_2_Outcomes.md)).

### CIS Controls — prioritized baseline

CIS publishes **18 Controls**, each broken into **Safeguards**, ordered to favor high-value defensive work. Implementation **Groups (IG1 / IG2 / IG3)** scale ambition by enterprise maturity and resources (start with IG1 essentials).

Public Control titles (v8 / v8.1 family — confirm wording on CIS’s list page):

1. Inventory and Control of Enterprise Assets  
2. Inventory and Control of Software Assets  
3. Data Protection  
4. Secure Configuration of Enterprise Assets and Software  
5. Account Management  
6. Access Control Management  
7. Continuous Vulnerability Management  
8. Audit Log Management  
9. Email and Web Browser Protections  
10. Malware Defenses  
11. Data Recovery  
12. Network Infrastructure Management  
13. Network Monitoring and Defense  
14. Security Awareness and Skills Training  
15. Service Provider Management  
16. Application Software Security  
17. Incident Response Management  
18. Penetration Testing  

**CIS vs 800-53:** CIS is easier to sequence as a backlog (“do Control 1–8 before exotic tooling”). 800-53 is denser and maps cleanly into formal authorization and many contract questionnaires. Mature programs often **implement** with CIS/cloud baselines and **trace** to 800-53/CSF for auditors.

### What not to do

| Anti-pattern | Better |
|--------------|--------|
| Redistributing paid CIS PDF content into the wiki | Link official download; summarize locally |
| Claiming “800-53 complete” without a baseline/overlay | Name the baseline and inheritance |
| Mapping theater (spreadsheet, no owners) | One mapping owner; regenerate from source of truth |
| Ignoring SR / supply chain until a vendor breach | Treat SR and CIS Service Provider Management early |

## Apply

Implementation habits for the catalog you actually run:

1. Choose a **primary** day-to-day catalog (CIS IG level or cloud baseline) for your team.  
2. Map five live engineering practices to either a CIS Control number or an 800-53 family.  
3. Mark which of those are **inherited** (platform/IdP/logging) vs owned by the app team.  
4. For one gap (for example Audit Log Management / AU), write the evidence location (system + retention).  
5. Open [6](./6_SOC2_And_Evidence.md) when the next question is “will this satisfy Type II?”

### Self-check

- [ ] State the job of 800-53 vs CIS in one sentence each  
- [ ] Name at least eight 800-53 family IDs from memory  
- [ ] Explain Implementation Groups without reading the PDF  
- [ ] Give one reason not to run two full catalogs as two programs  
- [ ] Contrast 800-53 catalog vs 800-53B baseline in one sentence  

## Next

- [ISO 27001 ISMS literacy](./5_ISO_27001_ISMS_Literacy.md)

## Further reading

- [NIST SP 800-53 Rev. 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final)
- [NIST SP 800-53B — Control Baselines](https://csrc.nist.gov/pubs/sp/800/53/b/upd1/final)
- [NIST SP 800-53A — Assessing Security and Privacy Controls](https://csrc.nist.gov/pubs/sp/800/53/a/r5/final)
- [NIST SP 800-53 controls downloads / OSCAL](https://csrc.nist.gov/projects/risk-management/sp800-53-controls/downloads)
- [CIS Controls](https://www.cisecurity.org/controls)
- [CIS Controls list (18 Controls)](https://www.cisecurity.org/controls/cis-controls-list)
- [CIS Controls v8.1 overview](https://www.cisecurity.org/controls/v8-1)
- [FedRAMP](https://www.fedramp.gov/)
