# SOC 2 and evidence

[← GRC](./README.md)

> **Progression:** concept → advanced → apply.

How **SOC 2** assurance works at literacy level, and what engineers must produce as **evidence**. Nesting: [1](./1_NIST_ISO_And_ATTCK.md). ISMS cousin: [5](./5_ISO_27001_ISMS_Literacy.md). Not legal advice and not CPA firm training.

## Concept

**SOC 2** (System and Organization Controls 2) is an AICPA examination of controls at a **service organization** relevant to one or more **Trust Services Criteria** categories:

| Category | Everyday meaning |
|----------|------------------|
| **Security** | System is protected against unauthorized access / use (required baseline for SOC 2; uses the *common criteria*) |
| **Availability** | System is available for operation and use as committed |
| **Processing integrity** | System processing is complete, valid, accurate, timely, authorized |
| **Confidentiality** | Confidential information is protected as committed |
| **Privacy** | Personal information is collected, used, retained, disclosed, disposed per commitments / criteria |

Authoritative criteria name in current AICPA materials: **2017 Trust Services Criteria for Security, Availability, Processing Integrity, Confidentiality, and Privacy (With Revised Points of Focus — 2022)**.

**Security** is evaluated with the **common criteria** (control environment, information and communication, risk assessment, monitoring, and control activities including access, operations, change, risk mitigation). Other categories add category-specific criteria (A / PI / C / P series) **on top of** the common criteria.

### Type I vs Type II

| Type | What the report covers |
|------|------------------------|
| **Type I** | Fairness of management’s system description and **suitability of design** of controls as of a **point in time** |
| **Type II** | Type I topics **plus operating effectiveness** of controls over a **period of time** (often ~6–12 months) |

Customers buying SaaS usually want **Type II**. Type I can be a milestone; it is not a substitute for sustained operation.

### SOC 1 vs SOC 2 vs SOC 3 (do not mix them)

| Report | About | Typical audience |
|--------|-------|------------------|
| **SOC 1** | Controls relevant to **user entities’ internal control over financial reporting (ICFR)** | User entities and *their* financial-statement auditors |
| **SOC 2** | Controls vs **Trust Services Criteria** (security, availability, processing integrity, confidentiality, privacy) — detailed description + tests | Management, customers, and other specified knowledgeable parties (usually restricted use) |
| **SOC 3** | Same TSC subject matter as SOC 2, but a **general-use** summary opinion **without** the detailed test descriptions | Broader / public-facing assurance summary |

Engineers asked for “our SOC” almost always mean **SOC 2 Type II**. A SOC 1 does not answer security-questionnaire TSC questions; a SOC 3 does not replace the detailed SOC 2 package buyers review under NDA.

Management’s **system description** is evaluated against AICPA **description criteria** (currently the 2018 Description Criteria with 2022 revised implementation guidance) — separate from the Trust Services Criteria used to judge control design/effectiveness.

```text
Scope + commitments + system description (description criteria)
  → design controls to TSC
    → operate continuously
      → collect evidence across the period
        → Type II examination → report to customers (under NDA typically)
```

## Advanced

### What engineers actually own

Auditors sample whether controls **operated**. Slides do not operate. Useful evidence is boring and timestamped:

| Control theme | Evidence examples |
|---------------|-------------------|
| Access | Joiner/mover/leaver tickets; access reviews; IdP logs; MFA enforcement configs |
| Change | PRs, approvals, deploy records, change tickets linked to releases |
| Vulnerability | Scan results, ticket SLAs, exception records |
| Logging / monitoring | Retention configs, alert runbooks, on-call pages with resolution |
| Availability | Backup restore tests, incident tickets, status-page history |
| Vendors | Due-diligence records, DPAs, shared-responsibility notes |

Delivery-shaped evidence often lives next to the pipeline: [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).

### Scope and carve-outs

| Idea | Why it bites |
|------|--------------|
| System boundary | Only in-scope services/systems appear in the description |
| Subservice organizations | Cloud/IdP providers may be carved out or inclusive; inheritance must be explicit |
| Complementary user entity controls (CUECs) | Things *customers* must do; do not silently assume them |

If production deploy keys live in a chat channel, no TSC mapping spreadsheet will save the period.

### SOC 2 vs ISO 27001 vs “being secure”

| Artifact | Job |
|----------|-----|
| SOC 2 report | Assurance for *users of the service* about controls vs TSC |
| ISO/IEC 27001 certificate | Conformity of an *ISMS* to 27001 requirements for a scope |
| Actual security | Reduces risk whether or not a report exists |

Many companies pursue both. Map once; generate two narratives ([1](./1_NIST_ISO_And_ATTCK.md)).

### Anti-patterns

- Screenshot week before the auditor arrives  
- “Security is in scope” with Availability promised in the SLA but no restore tests  
- Policy dated last year; tickets show exceptions with no risk acceptance  
- Treating ATT&CK coverage charts as SOC evidence ([1](./1_NIST_ISO_And_ATTCK.md))  

## Apply

Implementation habits that make Type II evidence boring and real:

1. Name which TSC categories your product must support (Security plus…?).  
2. For Access and Change, write the **system of record** for evidence (IdP, Git forge, ticketing).  
3. Pick one control and list samples an auditor could pull for a 6-month Type II window.  
4. Confirm backup restore or failover was **tested** in the last period if Availability is in scope.  
5. Link one Handbook Security gate you already run to a TSC theme (change / vuln / access).

### Self-check

- [ ] Recite the five Trust Services Categories  
- [ ] Contrast Type I vs Type II in one sentence each  
- [ ] Explain why Security uses common criteria  
- [ ] Give three engineer-owned evidence types that are not PDFs of policies  
- [ ] Contrast SOC 1, SOC 2, and SOC 3 in one line each  

## Next

- [NICE workforce literacy](./7_NICE_Workforce_Literacy.md)

## Further reading

- [AICPA — SOC 2 overview](https://www.aicpa-cima.com/topic/audit-assurance/audit-and-assurance-greater-than-soc-2/)
- [AICPA — System and Organization Controls (SOC suite)](https://www.aicpa-cima.com/resources/landing/system-and-organization-controls-soc-suite-of-services)
- [2017 Trust Services Criteria (With Revised Points of Focus – 2022)](https://www.aicpa-cima.com/resources/download/2017-trust-services-criteria-with-revised-points-of-focus-2022)
- [2018 SOC 2 Description Criteria (With Revised Implementation Guidance – 2022)](https://www.aicpa-cima.com/resources/download/2018-soc-2-description-criteria-with-revised-implementation-guidance-2022)
- [DevOps-Handbook — Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security)
