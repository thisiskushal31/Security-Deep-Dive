# CIA and risk

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Security work starts by naming what you protect and how bad it is if that protection fails. The classic three security objectives are confidentiality, integrity, and availability. Risk is how you decide which failures matter most and what to do about them.

## Concept

## CIA triad

- **Confidentiality** — Preserve authorized restrictions on information access and disclosure, including means for protecting personal privacy and proprietary information.
- **Integrity** — Guard against improper information modification or destruction, and ensure information non-repudiation and authenticity. That covers data integrity (data not altered without authorization in storage, processing, or transit) and system integrity (the system still does what it is supposed to do).
- **Availability** — Ensure timely and reliable access to and use of information.

![CIA triad](../Assets/Foundations/GFG_CIA_Triad.webp)

*Image: [Understanding CIA Triad](https://www.geeksforgeeks.org/cybersecurity/the-cia-triad-in-cryptography/).*

| Objective | Everyday example | Typical failure |
|-----------|------------------|-----------------|
| Confidentiality | Access-controlled API; encrypted field | Public object store; secret in logs |
| Integrity | Signed artifact; checksum; immutable log | Unsigned deploy; silent data edit |
| Availability | Redundancy; capacity; rate limits | Ransomware lockout; capacity exhaustion |

![Confidentiality controls concept](../Assets/Foundations/GFG_Confidentiality.webp)

*Image: [Understanding CIA Triad — confidentiality](https://www.geeksforgeeks.org/cybersecurity/the-cia-triad-in-cryptography/).*

![Availability concept](../Assets/Foundations/GFG_Availability.webp)

*Image: [Understanding CIA Triad — availability](https://www.geeksforgeeks.org/cybersecurity/the-cia-triad-in-cryptography/).*

Pushing confidentiality to the extreme (systems so locked that operators cannot work) often harms availability. Risk decisions balance the three for the asset’s data class and mission.

### McCumber cube (awareness)

The CIA objectives can be viewed across **states of information** (storage, transmission, processing) and **safeguard types** (technology, policy/practice, human factors). Use it as a completeness check: “Did we cover this data class at rest, in transit, and in use — with tech, process, and people?” Detail on states: [6](./6_Assets_Data_States_And_Assurance.md).

### Cross-cutting habits

| Habit | Meaning |
|-------|---------|
| Adversarial thinking | Ask how a motivated actor would abuse the design, not only how happy users use it |
| Systems thinking | Controls interact; fixing one layer can move risk to another |
| Defense in depth | Assume one control fails ([2](./2_Controls_And_Physical.md)) |

### Related properties

| Property | Meaning |
|----------|---------|
| Authenticity | Confidence in the origin of data or an artifact |
| Non-repudiation | A party cannot credibly deny having performed an action. In practice this rests on trustworthy identity, integrity evidence (for example digital signatures and protected audit logs), and key custody — see [8](./8_Cryptographic_Solutions_Literacy.md) |
| Privacy | Appropriate collection, use, and minimization of personal data — literacy in [17](./17_Privacy_Literacy.md) |
| Trust vs trustworthiness | Trust is a decision to rely; trustworthiness is evidence that a system deserves that reliance ([11](./11_Security_Models_And_Architecture_Literacy.md)) |

### Data classification

| Class (example ladder) | Handling idea |
|------------------------|---------------|
| Public | No confidentiality requirement for the content; still protect integrity of the official copy |
| Internal | Need-to-know inside the organization |
| Confidential | Limited roles; encrypt in transit and at rest per policy |
| Restricted / regulated | Strictest access, retention, and review rules |

Classify the asset before arguing about controls. Wrong class leads to wrong spend.

## AAA (short map)

**Authentication** establishes identity. **Authorization** decides what that identity may do. **Accounting** records what happened so you can investigate and support non-repudiation.

Detail: [7_AAA_And_Access_Fundamentals](./7_AAA_And_Access_Fundamentals.md). Federation and Zero Trust product depth: [Identity](../Identity/README.md).

## Advanced

## Risk vocabulary

| Term | Meaning |
|------|---------|
| Asset | Something of value (data, service, reputation, money, safety) |
| Threat | A circumstance or event with the potential to adversely impact an asset |
| Vulnerability | A weakness that a threat could exploit |
| Risk | The effect of uncertainty on objectives — often discussed as a combination of the likelihood that a threat exploits a vulnerability and the resulting impact |
| Control | A safeguard or countermeasure that reduces likelihood or impact |

```text
Threat + Vulnerability + Asset  →  Risk
Control reduces likelihood and/or impact
Residual risk remains after controls
```

## Inherent vs residual risk

| Term | Meaning |
|------|---------|
| Inherent risk | Risk before additional controls |
| Residual risk | Risk left after controls |
| Risk appetite / tolerance | How much residual risk leadership accepts |
| Risk register | Living list: scenario, score, owner, treatment, review date |

### Risk treatment

| Treatment | Meaning | Example |
|-----------|---------|---------|
| Mitigate | Reduce likelihood or impact with controls | Patch; MFA; segmentation |
| Avoid | Stop the activity that creates the risk | Do not store that data; remove the feature |
| Transfer / share | Shift some impact (does not remove the need for controls) | Contractual controls; insurance |
| Accept | Live with residual risk on purpose | Named owner, written reason, review or expiry date |

Acceptance without an owner or review date is not a treatment.

## Qualitative vs quantitative scoring

| Approach | Practice | When |
|----------|----------|------|
| Qualitative | Ordered scales (for example Low/Medium/High) for likelihood and impact | Most product and platform teams |
| Quantitative | Numeric loss estimates where the data exists | Larger programs, insurance, board reporting |

Comparable scores beat fake precision. Break ties with exposure (internet-facing before internal).

## Gap analysis

A **gap analysis** compares the **current** security posture to a **desired** state (policy, baseline, framework profile, or contract).

| Step | Practice |
|------|----------|
| Define target | What “good” means for this system or program |
| Measure current | Controls present, coverage, evidence |
| List gaps | Missing, weak, or unowned controls |
| Prioritize | Risk and exposure, not alphabetically |
| Assign owners | Every gap gets a name and a date |

The useful output is a prioritized backlog — not a guilt list. Framework nesting: [GRC](../GRC/README.md).

## Apply
1. Name the **asset** and **data class**.  
2. Name the **threat** and the **path**.  
3. Estimate **likelihood** and **impact**.  
4. Choose **controls** with an **owner** and a **review date**.  
5. State **residual risk** and the treatment.  
6. Keep **evidence** (tickets, configs, logs, scan results) if the decision will be audited later.

### Example

| Piece | Example |
|-------|---------|
| Asset | Customer email list (confidential) |
| Threat | Opportunistic discovery of a misconfigured store |
| Vulnerability | Object ACL allows public read |
| Impact | Confidentiality breach; notification and trust cost |
| Control | Block public ACL; private access path; access logging; alert on public ACL |
| Residual | Authorized roles can still read — accepted with access review |

## Defense in depth

Use layered controls so one failure is not total loss: identity, network/edge, host/runtime, application, data, then detect and recover. Control types: [2](./2_Controls_And_Physical.md).

## Related homes

- Wire / TLS → [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive)
- Pipeline gates and secrets → [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security)
- Design-time trade-offs → [System-Design Security-Tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security-Tradeoffs)
- Crypto pitfalls → [Cryptography](../Cryptography/README.md); literacy → [8](./8_Cryptographic_Solutions_Literacy.md)

## Self-check

- [ ] State confidentiality, integrity, and availability in one sentence each  
- [ ] Separate threat, vulnerability, and risk  
- [ ] Name four risk treatments  
- [ ] Rewrite the public-store example with inherent vs residual risk  
- [ ] Name one check from the McCumber view (state × safeguard) you would apply to backups  

## Next

- [Controls, physical, and deception](./2_Controls_And_Physical.md)

## Further reading

- [NIST SP 800-12 Rev. 1 — An Introduction to Information Security](https://csrc.nist.gov/pubs/sp/800/12/r1/final)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [NIST SP 800-30 Rev. 1 — Guide for Conducting Risk Assessments](https://csrc.nist.gov/pubs/sp/800/30/r1/final)
