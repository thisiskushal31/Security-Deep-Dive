# ISO 27001 ISMS literacy

[← GRC](./README.md)

> **Progression:** concept → advanced → apply.

How an **information security management system (ISMS)** thinks — establish, implement, maintain, continually improve. Nesting map: [1](./1_NIST_ISO_And_ATTCK.md). Control catalogs you may map into an ISMS: [4](./4_Control_Catalogs_800-53_And_CIS.md).

Literacy for engineers and program owners — **not** a dump of paid standard text and not auditor training. Deeper reading stays in Further reading.

## Concept

**ISO/IEC 27001** specifies **requirements** for an ISMS. An ISMS is the organization’s managed approach to information security risk: policies, processes, leadership, resources, controls, measurement, and improvement — not a single tool purchase.

Conformity means the organization has a system to manage risks related to the security of information it owns or handles, aligned with the principles in the standard. Organizations of any size/sector can use it.

That loop is commonly described with **PDCA**:

| Phase | ISMS sense |
|-------|------------|
| **Plan** | Context, scope, policy, risk assessment/treatment, objectives, resources |
| **Do** | Implement and operate the ISMS and selected controls |
| **Check** | Monitor, measure, audit, management review |
| **Act** | Correct, update risk treatment, continually improve |

**Certification** is optional. ISO does **not** certify organizations; accredited **external certification bodies** do. Many teams implement ISMS practices for internal benefit without seeking a certificate; others certify to reassure customers. Say “certified to ISO/IEC 27001:2022,” not a vague “ISO certified.”

```text
Context & leadership
  → plan (risks, objectives, Statement of Applicability)
    → operate (implement controls & processes)
      → evaluate performance
        → improve  → (repeat)
```

## Advanced

### Requirements vs Annex A controls

| Piece | Role |
|-------|------|
| **Clauses (management system requirements)** | Mandatory ISMS “operating system”: context, leadership, planning, support, operation, performance evaluation, improvement. Claiming conformity without these is empty. |
| **Annex A (information security controls)** | Reference set of controls. Organizations select applicable controls based on risk and document that choice. |
| **Statement of Applicability (SoA)** | Records which controls are applicable (and why / why not) and implementation status — the customization spine of the ISMS. |

ISO/IEC 27001:2022 revised Annex A (grouped in public explainers as organizational, people, physical, and technological themes). Exact control text belongs in the purchased standard — do not paste it into this wiki.

### Family documents (what to buy / cite)

| Document | Role |
|----------|------|
| **ISO/IEC 27001** | **Requirements** for the ISMS — the usual certification target |
| **ISO/IEC 27002** | **Guidance** on information security controls (helps implement Annex A themes; not the cert standard by itself) |
| **ISO/IEC 27000** | Overview / concepts for the ISMS family |
| **ISO/IEC 27005** (and related) | Risk management guidance often used alongside 27001 planning |

Paid standard text stays off this wiki; purpose and certification facts are enough for literacy. CSF↔ISO/IEC 27001 informative references are linked from [2](./2_NIST_CSF_2_Outcomes.md) when you need a crosswalk.

**Surveillance / recertification:** certificates are not forever. Expect ongoing internal audit, management review, and external surveillance — the same “continuous” muscle SOC 2 Type II needs ([6](./6_SOC2_And_Evidence.md)).

### Certification vs “we have controls”

| Claim | What it should mean |
|-------|---------------------|
| We run security controls | Technical/process safeguards exist |
| We have an ISMS | Those controls sit inside managed scope, risk treatment, measurement, and improvement |
| We are certified to ISO/IEC 27001:YYYY | An accredited body examined conformity for a defined scope |

A pile of CIS Safeguards without scope, SoA, internal audit, and management review is **not** an ISMS. An ISMS certificate without working engineering evidence is theater the next surveillance audit will notice.

### How this nests with CSF / 800-53 / SOC 2

| Framework | Typical use beside ISO |
|-----------|-------------------------|
| CSF | Outcomes language for leadership conversations |
| 800-53 / CIS | Detailed control catalogs mapped into the SoA / risk treatment |
| SOC 2 | Customer assurance report (US-heavy SaaS); can coexist with ISO certification |

Pick one **management system** story. Map catalogs into it. Do not run “ISO program” and “SOC program” as unrelated empires ([1](./1_NIST_ISO_And_ATTCK.md)).

## Apply

Implementation habits before you talk to a certification body:

1. Write your **ISMS scope** in one paragraph (org units, locations, services, exclusions).  
2. Name the **risk treatment** owner and the **management review** cadence.  
3. List five live controls and mark each: in SoA / inherited / gap.  
4. Decide whether certification is a **customer requirement** this year or internal practice only.  
5. Point evidence homes toward [6](./6_SOC2_And_Evidence.md) and delivery gates in [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).

### Self-check

- [ ] Define ISMS without saying “compliance checklist”  
- [ ] Explain PDCA in four bullets for security  
- [ ] Contrast Annex A selection with management-system clauses  
- [ ] State who issues ISO/IEC 27001 certificates (not ISO itself)  

## Next

- [SOC 2 and evidence](./6_SOC2_And_Evidence.md)

## Further reading

- [ISO/IEC 27001 — Information security management systems](https://www.iso.org/standard/27001)
- [ISO — Management system standards (certification note)](https://www.iso.org/management-system-standards.html)
- [ISO/IEC 27000 overview (family concepts)](https://www.iso.org/standard/27000)
