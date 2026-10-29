# NIST, ISO, ATT&CK

[← GRC](./README.md)

How common frameworks nest. This is **literacy for engineers and security program owners** — not legal advice, not a certification dump.

## Nesting (mental model)

| Layer | Job | Examples |
|-------|-----|----------|
| **Law / contract** | What you must do externally | Privacy regs, customer DPAs |
| **Management system** | How the org runs security | ISO/IEC 27001 ISMS |
| **Control frameworks** | Catalogs of controls to implement | NIST SP 800-53, CIS Controls |
| **Cybersecurity outcomes** | Functions / outcomes language | [NIST CSF](https://www.nist.gov/cyberframework) (Identify→Recover) |
| **Workforce** | Roles and skills | [NICE Framework](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center) |
| **Threat techniques** | How adversaries behave | [MITRE ATT&CK](https://attack.mitre.org/) |
| **Assurance reports** | Evidence for customers | SOC 2 Type I/II (AICPA TSC) |

Pick the **customer-facing** report (often SOC 2 or ISO 27001) and map your engineering controls upward. Do not run five overlapping control catalogs without a mapping owner.

## ATT&CK in the program

| Use | Practice |
|-----|----------|
| Detection engineering | Map alerts to techniques; find coverage gaps |
| Purple / red exercises | Agree on technique IDs in scope |
| IR retrospectives | Tag what happened in ATT&CK language |

ATT&CK is **not** a compliance framework. It does not replace ISO/NIST controls; it describes adversary behavior those controls should address.

## SOC 2 / ISO 27001 — engineer grain

| Idea | Meaning for builders |
|------|----------------------|
| Control exists | Written + implemented (not a slide) |
| Evidence | Logs, tickets, pipeline artifacts, access reviews |
| Scope | Systems and data in the report boundary |
| Continuous | Type II / surveillance audits need ongoing operation |

Delivery evidence often comes from gates and change tickets: [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security).

## Related home (do not rewrite here)

- Wire / TLS → Networks-Deep-Dive `Security/`
- Pipeline gates → DevOps-Handbook `Security/`
- Design trade-offs → System-Design `Security-Tradeoffs/`

## Further reading

- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [ISO/IEC 27001 overview (ISO)](https://www.iso.org/standard/27001)
