# Privacy literacy

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Privacy is related to confidentiality but is not the same thing. Security asks “who can access?” Privacy also asks “should we collect, keep, and use this personal data at all — and for what purpose?”

Policy boundary: [4](./4_Security_Policy_Governance_And_Roles.md). Data states: [6](./6_Assets_Data_States_And_Assurance.md). Privacy-threat categories at design time: LINDDUN awareness in [12](./12_STRIDE_And_Threat_Modeling.md). Program/legal catalogs: [GRC](../GRC/README.md).

## Concept

## Security vs privacy

| | Security | Privacy |
|--|----------|---------|
| Core question | Protect CIA of assets | Fair, lawful, proportionate use of personal data |
| Typical tools | Access control, encryption, logging | Minimization, purpose limitation, retention, rights handling |
| Failure example | Breach of encrypted DB with keys exposed | Lawful access used for an undeclared purpose |

You can have strong encryption and still violate privacy. You can have privacy policies and still fail security.

## Core privacy ideas (literacy)

| Idea | Meaning |
|------|---------|
| Personal data / PII | Data that identifies or can reasonably identify a person |
| Purpose limitation | Collect for stated purposes; do not quietly reuse for new ones |
| Data minimization | Collect and keep the least you need |
| Retention | Delete or anonymize when the purpose ends |
| Notice / transparency | People understand what is collected and why |
| Individual rights | Access, correction, deletion, or objection where policy/law grants them |
| Consent vs other bases | Consent is one lawful basis — not the only one in every regime |

This note is literacy, not legal advice. Map requirements with counsel and [GRC](../GRC/README.md).

## Advanced

## Technical overlaps

| Control | Privacy angle |
|---------|---------------|
| Access control / logging | Limit and evidence who saw personal data |
| Encryption | Protect confidentiality of personal data at rest/in transit |
| Tokenization / masking | Reduce exposure in lower environments and support tools |
| Anonymization / aggregation | Enable analytics without unnecessary identity |
| DLP / egress control | Reduce accidental disclosure |

## Apply
1. Name the personal data fields before shipping a feature.  
2. State the purpose and retention.  
3. Prefer minimization over “collect everything and decide later.”  
4. Threat-model privacy harms (LINDDUN) when personal data is central ([12](./12_STRIDE_And_Threat_Modeling.md)).  
5. Align breach notification clocks with IR ([Defensive-Ops/2](../Defensive-Ops/2_Incident_Response.md)).

## Self-check

- [ ] Contrast privacy and confidentiality in one sentence each  
- [ ] Name purpose limitation, minimization, and retention  
- [ ] Give one feature where encryption alone would not fix a privacy issue  

## Next

- [Adversary models, Kill Chain, and ATT&CK](./18_Adversary_Models_Kill_Chain_And_ATTCK.md)

## Further reading

- [NIST Privacy Framework](https://www.nist.gov/privacy-framework)
- [OWASP — Privacy](https://owasp.org/www-community/Privacy)
- [NIST SP 800-122 — Guide to Protecting the Confidentiality of Personally Identifiable Information (PII)](https://csrc.nist.gov/pubs/sp/800/122/final)
