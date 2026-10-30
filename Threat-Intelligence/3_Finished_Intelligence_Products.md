# Finished intelligence products

[← Threat-Intelligence](./README.md)

> **Progression:** concept → advanced → apply.

Audience-shaped CTI writing, TLP handling, and producer/consumer hygiene. Analytic method: [1](./1_Analytic_Tradecraft.md). Models: [2](./2_Intrusion_Analysis_Models.md). Machine packaging: [4](./4_STIX_And_TAXII_Practice.md).

## Concept

**Finished intelligence** is analyzed, audience-ready product — not a raw IOC dump. NIST SP 800-150’s “threat intelligence reports” type sits above indicators: enriched prose that supports a decision ([1](./1_Analytic_Tradecraft.md)).

| Level | Audience | Typical product | Decision horizon |
|-------|----------|-----------------|------------------|
| **Strategic** | Exec / board / risk | Outlook, adversary intent & capability vs your sector | Quarters–years |
| **Operational** | SOC lead / IR / detection eng | Campaign / intrusion set brief, TTP focus | Days–weeks |
| **Tactical / technical** | Analysts / tooling | Indicator cards, YARA/Snort pointers, STIX bundles | Hours–days |

Same facts, different **so what**. Strategic never leads with hashes; tactical never opens with geopolitics.

### Product anatomy (minimum viable)

| Element | Role |
|---------|------|
| **TLP / handling** | Who may see and reshare ([TLP 2.0](#tlp-20-first) below) |
| **Scope / PIR link** | Which requirement this answers |
| **BLUF / key judgments** | Bottom line up front — ICD 203: clear argumentation |
| **What happened** | Observations separated from judgments ([1](./1_Analytic_Tradecraft.md)) |
| **So what / implications** | Customer relevance (ICD 203) |
| **Confidence & alternatives** | Uncertainties; analysis of alternatives |
| **Recommendations** | Detect / deny / disrupt / hunt — tied to Kill Chain CoAs ([2](./2_Intrusion_Analysis_Models.md)) |
| **IOCs / ATT&CK** | Machine-usable appendix or STIX ([4](./4_STIX_And_TAXII_Practice.md)) |
| **Sources / provenance** | Quality of sources (ICD 203); update/retract path |
| **Feedback ask** | Did this help the decision? |

### BLUF example shapes

| Level | Opening line pattern |
|-------|----------------------|
| Strategic | “We assess with moderate confidence that …; recommend … this quarter.” |
| Operational | “Campaign C uses T1566→T1059 against finance VDI; hunt queries and mail rules attached.” |
| Tactical | “Block domain X (expires …); YARA rule id …; false-positive notes …” |

## Advanced

### TLP 2.0 (FIRST)

Traffic Light Protocol 2.0 labels **sharing boundaries**. Mis-marked CTI either leaks sensitive detail or starves defenders who could have used CLEAR/GREEN intel. FIRST defines **four** base labels (RED, AMBER, GREEN, CLEAR); **AMBER+STRICT** is the AMBER form that limits sharing to the recipient’s organization only (no clients).

| Label | Sharing rule (FIRST summary) |
|-------|------------------------------|
| **TLP:CLEAR** | Recipients may share without restriction (subject to copyright / public-release rules) |
| **TLP:GREEN** | Limited — peers/partner orgs in the **community**; not via publicly accessible channels |
| **TLP:AMBER** | Limited — need-to-know inside the organization **and its clients** |
| **TLP:AMBER+STRICT** | Same as AMBER but **organization only** (source must specify `+STRICT`) |
| **TLP:RED** | Individual recipients only — no further disclosure |

**Producer usage (FIRST):**

- Mark every email / document / product with TLP; put the label where readers see it first (subject line, header, title page).  
- If different paragraphs need different labels, mark **by section** — the most restrictive applicable label wins for mixed content when in doubt.  
- Choose the *least restrictive* label that still protects the equities (over-RED everything trains people to ignore markings).  
- Recipients must understand TLP before you send AMBER/RED.

**Consumer usage:**

- Do not redistribute above the label.  
- AMBER → clients only when they need it to defend themselves; AMBER+STRICT → **not** to clients.  
- RED → no forwarding; brief verbally if needed under the same constraint.  
- When combining sources, the **strictest** TLP on incorporated material governs the derivative product unless you strip/re-source the sensitive parts.

**Chatham House vs TLP:** Chatham House Rule is about *identity of speakers* in a discussion; TLP is about *document redistribution*. Do not treat them as interchangeable.

### NIST 800-150 — producing and consuming reports

| Producer habit | Consumer habit |
|----------------|----------------|
| Enrich with context (intended use, related indicators) | Evaluate source reputation and freshness before auto-block |
| Provide update and **retract** paths for bad indicators | Prefer TTP/reports over naked IOC floods when capacity is limited |
| Align shares to MOUs / sharing rules / legal-privacy review | Preserve provenance when re-hosting internally |
| Prefer PII-free indicators; redact irrelevant personal data | Map inbound intel to PIRs — discard or archive off-PIR noise |
| Establish trust and agreements **before** the incident | Feed detection/IR tickets with clear expiry and FP notes ([5](./5_Detection_Oriented_Handoff.md)) |

**Sensitive information:** CUI/PII may appear in phishing samples, disk forensics, or packet captures. Policies should tell analysts how to recognize PII, when to redact, and when focusing on hash/port/registry indicators avoids the issue entirely.

### Confidence and estimative language

ICD 203 requires products to **express and explain uncertainties** on major judgments — both **likelihood** (how probable is the event/assessment?) and **confidence** (how strong is the evidence base?). Many shops use a three-tier confidence label — that wording is **team convention**, not an ICD word list:

| Common confidence label | Typical meaning in CTI shops |
|-------------------------|------------------------------|
| High | Multiple reliable sources or strong technical corroboration; alternatives weak |
| Moderate | Plausible and partially corroborated; some gaps |
| Low | Fragmentary, single-source, or contested |

**Likelihood** (estimative words) answers a different question — e.g. “We assess it is *likely* that …” vs “we have *high confidence* in this assessment.” Do not mix them: you can have high confidence that something is *unlikely*. Always **explain why**.

### Consumers and stakeholder fit (shop floor)

| Consumer | What they need from CTI |
|----------|-------------------------|
| SOC / detection | Actionable TTPs/IOCs with expiry, FP notes, ATT&CK ([5](./5_Detection_Oriented_Handoff.md)) |
| IR | Campaign scope, related infra, what “done” looks like |
| VM / patch owners | In-the-wild exploitation context for CVE priority |
| GRC / risk / board | Strategic so-what, not hash lists |
| ISACs / peers | Marked shares that meet community rules; strip irrelevant PII |
| Leadership | BLUF + decision options + uncertainty |

**ISAC / community habit:** join for *sector* signal, contribute when policy allows, treat inbound shares with the same source evaluation as vendor blogs ([1](./1_Analytic_Tradecraft.md)). Membership is not a substitute for PIRs.

### Product anti-patterns

| Anti-pattern | Fix |
|--------------|-----|
| IOC paste with no so-what | Add BLUF + ATT&CK + recommended CoA |
| Strategic memo that is a CVE list | Elevate to capability/intent/impact |
| Copy-paste vendor attribution as fact | Re-state as “vendor assesses…”; run ACH if you adopt it |
| Eternal indicators | Expiry, revisit, retract |
| TLP:RED on everything | Calibrate; over-restriction kills sharing value |
| No PIR link | If no requirement owns it, why did you write it? |

### Worked outline — operational campaign brief

```text
TLP:AMBER
PIR: Are invoice-themed kits reaching finance VDI?
BLUF: Yes — moderate confidence; delivery via …; recommend mail + EDR hunts this week.
Judgments: …
Observations: …
Kill Chain / ATT&CK table: …
Diamond summary: adversary (cluster), capability, infra, victim scope
Recommendations: Detect / Deny / Hunt (owners, due dates)
Appendix: STIX 2.1 bundle id … | indicator expiry …
Sources: …
Feedback: reply-all to CTI with FP / TP counts by Friday
```

## Apply

1. Rewrite one public threat blog as a one-page **operational** brief with BLUF, confidence, ATT&CK, and three recommendations.  
2. Mark the same brief TLP:GREEN vs TLP:AMBER+STRICT — what content must change or drop?  
3. Draft a five-bullet **strategic** note for leadership from the same facts (no hashes in the body).  
4. Produce a **tactical** card: five indicators, each with type, context, expiry, and suggested action.  
5. Score a product you received against ICD 203’s nine tradecraft standards ([1](./1_Analytic_Tradecraft.md)).  
6. Write the retract email you would send if two of those indicators were false positives.

### Self-check

- [ ] Distinguish strategic, operational, and tactical products by audience and horizon  
- [ ] List the minimum elements of a finished CTI product  
- [ ] Name FIRST’s four base TLP labels and what AMBER+STRICT adds  
- [ ] Explain producer vs consumer TLP duties  
- [ ] State a confidence judgment with an explicit rationale (ICD uncertainty habit)  
- [ ] Distinguish likelihood wording from confidence  
- [ ] Match one product shape to SOC vs board vs ISAC consumers  
- [ ] Name two NIST hygiene habits for producing shares with PII risk  

## Next

- [STIX and TAXII practice](./4_STIX_And_TAXII_Practice.md)

## Further reading

- [FIRST — Traffic Light Protocol (TLP) 2.0](https://www.first.org/tlp/)
- [NIST SP 800-150 — Guide to Cyber Threat Information Sharing](https://csrc.nist.gov/pubs/sp/800/150/final)
- [ODNI — ICD 203 Analytic Standards (PDF)](https://www.odni.gov/files/documents/ICD/ICD-203.pdf)
- [CISA — Automated Indicator Sharing (AIS)](https://www.cisa.gov/topics/cyber-threats-and-advisories/information-sharing/automated-indicator-sharing-ais)
