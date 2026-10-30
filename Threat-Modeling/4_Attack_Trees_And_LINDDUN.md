# Attack trees and LINDDUN

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

Goal-centric attack decomposition and privacy threat elicitation in enough depth to run without leaving this note. STRIDE/DFD: [2](./2_DFD_And_STRIDE_In_Practice.md). PASTA Stage VI: [3](./3_PASTA_Staged_Walkthrough.md). Privacy literacy: [Foundations/17](../Foundations/17_Privacy_Literacy.md).

## Concept

### Attack trees

An **attack tree** is a diagram of attacks on a system in tree form (SEI). The **root** is the attacker’s goal. Children are alternative (**OR**) or required (**AND**) steps. Leaves are concrete conditions or actions you can mitigate or accept.

| Prefer an attack tree when | Example root |
|----------------------------|--------------|
| One high-value outcome dominates | “Exfiltrate all customer PII from prod” |
| Multi-step paths must be compared | “Issue fraudulent payout without detection” |
| PASTA Stage VI / abuse analysis | “Become the collection API” |

```text
Goal: Exfiltrate customer PII                    (ROOT)
├─ OR A: Steal DB credentials
│   ├─ AND phish operator with prod access
│   └─ AND reuse creds before rotation
├─ OR B: Abuse application IDOR
│   └─ enumerate object IDs without authZ
└─ OR C: Compromise encrypted backups
    ├─ AND obtain backup blob
    └─ AND obtain backup key
```

Annotate leaves with: difficulty, required privileges, whether a control already blocks the path, residual after mitigation.

### LINDDUN (privacy threat types)

**LINDDUN** is a privacy threat-modeling framework. Like STRIDE, it is a mnemonic: threat categories are coded in the name. It starts from a **DFD**, then systematically asks privacy questions STRIDE’s security properties do not fully cover ([linddun.org](https://linddun.org/threat-types/); SEI method summary).

| Type | One-line meaning |
|------|------------------|
| **Linking** | Associate data items or actions to learn more about a person/group |
| **Identifying** | Learn who someone is (leak, deduction, inference) |
| **Non-repudiation** | Attribute a claim to an individual (loss of plausible deniability) |
| **Detecting** | Infer involvement by observing that data/activity *exists* |
| **Data disclosure** | Excessively collect, store, process, or share personal data |
| **Unawareness / unintervenability** | People not informed or unable to control processing |
| **Non-compliance** | Deviate from required practices, standards, or law |

Run LINDDUN **with or after** STRIDE when personal data, profiling, or regulated processing is in scope.

## Advanced

### Building attack trees that survive review

| Habit | Detail |
|-------|--------|
| One root per tree | Mixed goals become unreadable |
| Label AND vs OR explicitly | Wrong logic → wrong mitigations |
| Stop at actionable leaves | “Be clever” is not a leaf |
| Mark mitigated leaves | Shows residual paths at a glance |
| Prefer depth on likely paths | Manifesto: avoid overfocus on exotic adversaries |
| Pair with misuse cases | Out-of-order, repeated, concurrent valid workflows |
| Feed PASTA VI / tickets | Trees without owners are wall art |
| Apply the actionable-threat test at each leaf | “Would we file a bug/design change?” — [1](./1_Workshop_Process.md) |
| Keep crown-jewel assets at the root | Stealing creds is usually a child path, not the goal |

**Hybrid use (SEI):** some programs build attack trees under STRIDE categories or score leaves with CVSS when leaves map to known vulns — keep design ranking and CVE severity distinct ([2](./2_DFD_And_STRIDE_In_Practice.md)).

**Authorized analysis only** for any simulation against real systems — [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

### LINDDUN type depth (what to look for)

#### Linking — “connecting the dots”

Associating items/actions via identifiers (username, IP, device), combinations (browser fingerprint, location trace), profiling, or **group** profiles. Problematic when it enables inference, singling out, or profile building — even without a legal name. Triggered by rich data disclosure.

Examples: suspend a pseudonymous hate-poster without knowing who they are; raise prices when the same fingerprint revisits a flight search; infer Alice works at company C from office-hours location; neighborhood group risk raises Carol’s insurance.

#### Identifying — “who’s who?”

Revealing identity via identified data (system keeps the link) or identifiable data (small anonymity set, revealing attributes). Related to linking; drives unawareness/non-compliance issues.

Examples: forced real-name registration to read news; average-salary query on a team of one woman; feedback form with enough detail to uniquely identify a car buyer.

#### Non-repudiation — privacy sense

Evidence (logs, signatures, metadata, watermarks, read receipts) that prevents deniability. **Security** non-repudiation is often desirable; **privacy** non-repudiation can harm whistleblowers or people in hostile legal contexts. Call the tension explicitly on the model.

Examples: document authorship metadata; read notifications; sensitive app data usable as legal evidence.

#### Detecting

Knowing data *exists* without reading it — traffic observation, side-effect files, error messages that confirm a record (“insufficient access” on a celebrity rehab record).

#### Data disclosure

Excess against minimization: unnecessary types/granularity; excessive volume/frequency; unnecessary further processing/retention; excessive exposure to parties. Explicit (designed) or implicit (metadata, derived).

Examples: diet app recording heart rate it does not need; social posts exposing others; retaining email after unsubscribe; third-party trackers.

#### Unawareness & unintervenability

Insufficient transparency, feedback (impact on others), or intervenability (access, correction, settings).

#### Non-compliance

Unlawful processing; weak lifecycle management; weak cyber risk management — LINDDUN focuses on non-compliance that **derives from** the other privacy threats (e.g. DoS blocking data-subject rights; breach enabling identification).

### LINDDUN method shape (six steps — SEI)

1. Build / reuse DFD  
2. Map DFD elements to LINDDUN threat types (which types apply where)  
3. Elicit concrete threats (catalogs / threat trees help)  
4. Prioritize  
5. Elicit mitigations / privacy requirements  
6. Document and iterate  

Labor-intensive like STRIDE: threat count grows with complexity — scope manageable slices.

### Method chooser (keep it honest)

| Need | Prefer |
|------|--------|
| Security threats on a technical design | STRIDE-per-element ([2](./2_DFD_And_STRIDE_In_Practice.md)) |
| One attacker goal / multi-step path | **Attack tree** |
| Privacy properties and harms | **LINDDUN** |
| Business-impact staged program | PASTA ([3](./3_PASTA_Staged_Walkthrough.md)) |
| Org-wide critical assets | OCTAVE, then app STRIDE |
| Many teams continuous light models | VAST-style app + ops models |

Do not stack methods for appearance. State why each fits; record what it does **not** cover.

## Apply

1. Write one attacker goal; build an attack tree with ≥2 OR branches and ≥1 AND; mark mitigated leaves.  
2. File tickets for the residual path’s critical leaves.  
3. If personal data exists: DFD + LINDDUN pass for Linking, Identifying, Data disclosure, and Unawareness on one store and one egress.  
4. Document one security-vs-privacy non-repudiation tension and the chosen trade-off owner.  
5. Decide explicitly: “this change needed trees / LINDDUN / both / neither.”

### Self-check

- [ ] Draw OR vs AND correctly on a four-node tree  
- [ ] Explain when an attack tree beats STRIDE-per-element  
- [ ] Name all seven LINDDUN types with one example each  
- [ ] Contrast privacy non-repudiation with security non-repudiation  
- [ ] Recite LINDDUN’s high-level six-step shape  
- [ ] Give a rule for not stacking methods theatrically  

## Next

- [Tooling and closing the loop](./5_Tooling_And_Closing_The_Loop.md)

## Further reading

- [LINDDUN — Threat types](https://linddun.org/threat-types/)
- [LINDDUN — Privacy threat trees](https://linddun.org/threat-trees/)
- [SEI — Threat Modeling Methods Summary](https://insights.sei.cmu.edu/library/threat-modeling-a-summary-of-available-methods/)
- [OWASP — Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)
