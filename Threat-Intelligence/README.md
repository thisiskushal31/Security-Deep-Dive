# Threat intelligence

[← README](../README.md)

**CTI** (Cyber Threat Intelligence) turns adversary-related data into decisions — what to block, hunt, patch, or brief. This folder is practice depth: analytic tradecraft, intrusion models, finished products, STIX/TAXII sharing, detection handoff, and malware reverse engineering for CTI — beyond foundation literacy.

Literacy: [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md) · [Foundations/19](../Foundations/19_Threat_Intelligence_Literacy.md). Related ops: [Defensive-Ops](../Defensive-Ops/README.md) · [GRC ATT&CK](../GRC/1_NIST_ISO_And_ATTCK.md) · [Threat-Modeling](../Threat-Modeling/README.md) · [Vulnerability-Management](../Vulnerability-Management/README.md).

Each note follows **concept → advanced → apply**. Official Further reading only; diagrams live under [Assets/Threat-Intelligence](../Assets/Threat-Intelligence/README.md) when added.

```text
1 Tradecraft + PIRs → 2 Kill Chain / Diamond / ATT&CK
  → 3 Finished products + TLP → 4 STIX / TAXII / AIS
    → 5 Detection handoff → 6 Malware RE for CTI
```

## Topic files

| # | Topic |
|---|--------|
| 1 | [Analytic tradecraft and PIRs](./1_Analytic_Tradecraft.md) |
| 2 | [Intrusion analysis models](./2_Intrusion_Analysis_Models.md) |
| 3 | [Finished intelligence products](./3_Finished_Intelligence_Products.md) |
| 4 | [STIX and TAXII in practice](./4_STIX_And_TAXII_Practice.md) |
| 5 | [Detection-oriented intel handoff](./5_Detection_Oriented_Handoff.md) |
| 6 | [Malware reverse engineering for CTI](./6_Malware_Reverse_Engineering_For_CTI.md) |

## Learning path

`1 → 2 → 3 → 4 → 5 → 6`

## Topic map

| Area | Home |
|------|------|
| CTI def, PIRs/RFIs, intel cycle, collection sources, Tor literacy, ICD 203, SATs, ACH | 1 |
| Kill Chain + CoA, Diamond threads/groups, infra pivoting (pDNS/WHOIS/cert/hash), ATT&CK | 2 |
| Product levels, TLP 2.0, confidence vs likelihood, stakeholder/ISAC consumers | 3 |
| STIX SDO/SCO/SRO/Bundle, TAXII, AIS/MISP/TIP awareness, retract/update hygiene | 4 |
| Pyramid of Pain, malware triage, TIP enrich, handoff tickets, YARA, F3EAD awareness, purple loop | 5 |
| RE ladder, isolated lab, PE/strings/imports, Ghidra, behavioral/debug/sandbox, packing, net, productize | 6 |

## Related

- Adversary / ATT&CK literacy → [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md)
- CTI vocabulary → [Foundations/19](../Foundations/19_Threat_Intelligence_Literacy.md)
- SIEM / IR consumers → [Defensive-Ops](../Defensive-Ops/README.md)
- Framework nesting → [GRC/1](../GRC/1_NIST_ISO_And_ATTCK.md)
- Design-time abuse → [Threat-Modeling](../Threat-Modeling/README.md)
- Vuln program consumers → [Vulnerability-Management](../Vulnerability-Management/README.md)
