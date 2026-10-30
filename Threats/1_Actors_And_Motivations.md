# Actors and motivations

[← Threats](./README.md)

> **Progression:** concept → advanced → apply.

Who or what can cause harm, why they act, and how capability changes the response. Every later note (vectors, malware, AI) still starts here: **same impact language, different sources**. Technique catalogs: [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md) · [GRC ATT&CK](../GRC/1_NIST_ISO_And_ATTCK.md).

## Concept

A **threat** is any circumstance or event with the potential to adversely impact organizational operations and assets, individuals, other organizations, or the Nation through an information system via unauthorized access, destruction, disclosure, modification of information, and/or denial of service.

**Threat events** are caused by **threat sources**. A threat source is characterized as (i) the intent and method targeted at exploiting a vulnerability, or (ii) a situation and method that may accidentally exploit a vulnerability.

A **cyber threat** is activity intended to compromise the security of an information system by altering availability, integrity, or confidentiality — or to disrupt digital life more broadly. The **cyber threat environment** is the online space where that activity happens: networks, devices, and processes connected to the internet, plus the methods used against them.

**Cyber threat actors** are groups or individuals who, with malicious intent, aim to exploit weaknesses in a system or its operators to gain unauthorized access or otherwise affect data, devices, systems, networks, or the authenticity of information flowing to and from them. They can be physically anywhere and still affect systems elsewhere.

```text
Threat source  →  threat event  →  exploits vulnerability  →  adverse impact
```

Multiple sources can cause the same event (DoS attack, malicious admin, operator error, hardware fault, power failure).

## Advanced

### Four threat-source types (risk assessment)

| Type | Meaning |
|------|---------|
| **Adversarial** | Hostile cyber or physical attacks |
| **Accidental** | Human errors of omission or commission |
| **Structural** | Failures of organization-controlled resources (hardware, software, environmental controls) |
| **Environmental** | Natural and man-made disasters, accidents, and failures beyond organizational control |

For **adversarial** sources, assess **capabilities**, **intent**, and **targeting**. For non-adversarial sources, assess the **potential range of effects**.

Adversary-driven events are described with **tactics, techniques, and procedures (TTPs)**.

### Actor classes and primary motivations

| Actor | Primary motivation (typical) | Detail |
|-------|------------------------------|--------|
| Nation-state / state-sponsored | Geopolitical objectives | Often highest sophistication: dedicated resources, planning, coordination. Activities include espionage, pre-positioning or disrupting critical systems, shaping public discourse, building compromised-device networks, and sometimes financially motivated activity. May use commercial tools or private-sector/criminal relationships. |
| Organized crime / cybercriminals | Financial gain | Wide sophistication range. Organized groups add planning and support functions. Illegal markets for tools/services raise accessibility and campaign complexity (ransomware, fraud, access resale). **AI-era:** GenAI-assisted phishing/malware, deepfake fraud, and reported “malicious AI” tooling markets — still classic motives ([8](./8_AI_Era_Threats.md)). |
| Hacktivist | Ideology / publicity | Often lower sophistication than states or top crime groups; widely available tools. Effects may be reputational; sometimes financial or physical damage. |
| Unskilled / thrill-seeker | Curiosity, status, satisfaction | Public tools; opportunistic targeting of weak systems. |
| Insider | Discontent, revenge, profit, or negligence | Especially dangerous because of access inside security perimeters. May be malicious or accidental; may also aid external actors. |
| Shadow IT | Convenience (not always malice) | Unapproved systems/SaaS that expand surface and bypass governance — treat as an actor *class* for risk even when intent is not hostile. |
| Terrorist groups (awareness) | Ideological violence | Often rely on widely available tools; still relevant in national threat pictures. |

**Advanced persistent threat (APT)** refers to top-tier sophistication and skill: advanced techniques and complex, protracted campaigns. Usually reserved for nation-states or very proficient organized crime.

**Commercial cyber tools** sold as “law enforcement” capability can deliver malware, intercept communications, or steal data. Not all providers discriminate among buyers; actors may use them to raise sophistication or muddy attribution.

### Attributes

| Attribute | Why it matters |
|-----------|----------------|
| Internal vs external | Insiders skip many edge controls |
| Resources / funding | Custom malware, zero-days, dwell time, paid access |
| Sophistication / capability | Scripted spray vs patient multi-stage intrusion |
| Targeting | Opportunistic vs directed (sector, org, individual) |

Sophisticated actors sometimes use *simple* tools deliberately — they still work, and they complicate attribution.

### Motivation checklist (exam / program list)

| Motivation | Typical aim |
|------------|-------------|
| Financial gain | Ransom, fraud, theft, resale of access/data |
| Data exfiltration | Steal information for use or sale |
| Espionage | Long-term collection for geopolitical or competitive advantage |
| Service disruption | Deny or degrade availability |
| Blackmail / extortion | Pressure via stolen or encrypted data |
| Philosophical / political / ideological | Message, protest, narrative |
| Revenge | Harm by someone with a grievance |
| Disruption / chaos | Instability without a precise financial goal |
| War | Effects alongside national conflict |
| Ethical (claimed) | Unauthorized “testing” — still unauthorized without permission |

### What actors target (impact framing)

| Target class | Examples of harm |
|--------------|------------------|
| Devices | Phones, PCs, servers, IoT/OT — footholds for further activity |
| Information | IP, business secrets, personal data, credentials |
| Financial resources | Funds, crypto, payment fraud |
| Opinions / reputation | Misinformation, disinformation, malinformation, influence campaigns — increasingly **AI-amplified** (synthetic media, scaled personas); see [8](./8_AI_Era_Threats.md) |
| Safety | Disruption of industrial/medical/transport systems; abuse of personal IoT |

### Attribution, obfuscation, false flags (literacy)

**Attribution** is determining which actor is responsible. It supports defense, law enforcement, deterrence, and diplomacy — and is often hard.

**Obfuscation** hides identity, goals, techniques, or victims (covert channels, shared tooling, proxies).

**False-flag** activity mimics another actor’s known patterns so defenders mis-attribute. Nation-states and capable criminals are generally more adept at obfuscation than low-skill actors.

Do not invent org-specific actor names when ATT&CK groups and shared CTI already provide a technique language.

### Real cases (these already happened)

Short examples from official public reports so you know this is not theory. Links have the full story — this is **not** a how-to.

| What happened | Why it matters here | Where to read more |
|-----------------------|------------------------------|--------------------|
| **Long-dwell, high-capability intrusion via trusted software** | Shows nation-state-class **capability + targeting** and why APT language exists — not “custom malware only” | [CISA AA20-352a — SolarWinds-era supply-chain compromise advisory](https://www.cisa.gov/news-events/cybersecurity-advisories/aa20-352a) · [MITRE ATT&CK campaign C0024](https://attack.mitre.org/campaigns/C0024/) |
| **Crime at internet scale** | Financial motivation dominates complaint *loss* categories (BEC, investment fraud) while phishing dominates *volume* | [FBI IC3 Annual Report](https://www.ic3.gov/AnnualReport/Reports/) |
| **Insider / trusted-path risk** | Many high-impact events start with valid credentials or vendor trust — “external perimeter” is not the whole story | CCCS threat environment · notes [2](./2_Vectors_And_Surfaces.md) · [7](./7_Mitigations.md) |

## Apply

1. Name the **asset** and data class ([Foundations/1](../Foundations/1_CIA_And_Risk.md)).  
2. List plausible **adversarial and non-adversarial** sources (short list).  
3. For adversarial sources, note **capability / intent / targeting**.  
4. State **motivation** and pick matching controls (money → backups/MFA/fraud; espionage → classification/egress; disruption → availability).  
5. Move from “who” to “how” with ATT&CK when needed ([GRC](../GRC/1_NIST_ISO_And_ATTCK.md)).

### Self-check

- [ ] Define threat, threat source, threat event, cyber threat actor  
- [ ] Name four NIST threat-source types  
- [ ] Explain APT and why sophistication ≠ always custom malware  
- [ ] Contrast nation-state, crime, hacktivist, insider, shadow IT  
- [ ] Define attribution, obfuscation, and false flag in one line each  

AI-era capability boost (deepfakes, scaled influence, AI-assisted tooling): [8](./8_AI_Era_Threats.md). Technique language for AI-centered campaigns: [MITRE ATLAS](https://atlas.mitre.org/).

## Next

- [Vectors and attack surfaces](./2_Vectors_And_Surfaces.md)

## Further reading

- [NIST SP 800-30 Rev. 1 — Guide for Conducting Risk Assessments](https://csrc.nist.gov/pubs/sp/800/30/r1/final)
- [Canadian Centre for Cyber Security — Introduction to the cyber threat environment](https://www.cyber.gc.ca/en/guidance/introduction-cyber-threat-environment)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [CISA AA20-352a — APT / SolarWinds-era advisory](https://www.cisa.gov/news-events/cybersecurity-advisories/aa20-352a)
- [FBI IC3 — Annual Reports](https://www.ic3.gov/AnnualReport/Reports/)
- [CompTIA Security+ SY0-701 Exam Objectives](https://comptiacdn.azureedge.net/webcontent/docs/default-source/exam-objectives/comptia-security-sy0-701-exam-objectives-(6-0).pdf) (Domain 2.1)
