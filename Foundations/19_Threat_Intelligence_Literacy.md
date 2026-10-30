# Threat intelligence literacy

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Cyber threat intelligence (CTI) turns raw data about adversaries into **decisions** — what to block, hunt, patch, or brief. This is foundation vocabulary for SOC, IR, and CTI courses. Adversary models: [18](./18_Adversary_Models_Kill_Chain_And_ATTCK.md). Detection/response operations: [Defensive-Ops](../Defensive-Ops/README.md). Analytic depth beyond literacy: [Threat-Intelligence](../Threat-Intelligence/README.md).

## Concept

## Data vs information vs intelligence

| Layer | Meaning |
|-------|---------|
| Data | Raw observables (feeds of IPs, hashes, logs) |
| Information | Organized data (a report listing domains used in a campaign) |
| Intelligence | Analyzed, relevant, timely product that supports a decision |

A million “bad” IPs without context is data. “This group targets retailers with invoice phishing; three of our hosts resolved their loader domain today” is intelligence.

## Intelligence lifecycle

```text
Requirements → Collection → Processing → Analysis → Dissemination → Feedback
```

| Step | CTI example |
|------|-------------|
| Requirements | What decisions need support (hunt, block, exec brief)? |
| Collection | Feeds, malware reports, dark-web notes you are allowed to use, internal telemetry |
| Processing | Normalize, dedupe, enrich |
| Analysis | Who / what / so what / what next |
| Dissemination | Right audience, right format, right handling label |
| Feedback | Did the product help? Update requirements |

If nobody can state the **requirement**, everything downstream is expensive noise. Priority intelligence requirements (PIRs) are the short list of questions the program must answer first.

### Source and confidence (literacy)

| Habit | Why |
|-------|-----|
| Separate observation from inference | “Hash seen on host” ≠ “APT group X did this” |
| Note source type | Internal telemetry vs vendor report vs open reporting |
| Prefer corroboration | One unverified feed should not drive irreversible action |
| Track confidence | Low-confidence IOCs → hunt/watch; high-confidence + context → block |

## Advanced

## Levels of CTI

| Level | Audience / horizon | Example product |
|-------|--------------------|-----------------|
| Strategic | Leadership; months–years | Which industries and motives matter to us |
| Operational | Campaigns and intent; weeks–months | How this intrusion set usually operates |
| Tactical | TTPs; days–weeks | How they phish and persist — map to ATT&CK |
| Technical | IOCs; hours–days | Domains, hashes, IPs for block/hunt |

Technical intel expires fast (Pyramid of Pain). Tactical and operational intel ages slower.

| Focus | Prefer |
|-------|--------|
| **IOC** (IP, domain, hash, URL) | Fast block/hunt; short shelf life |
| **TTP** (behavior mapped to ATT&CK) | Detections and purple emulation; longer value |

An IOC without TTP context is often a one-shot block. A TTP without any observable may still drive hunting hypotheses.

## Sharing rules and formats

| Piece | Role |
|-------|------|
| **TLP** (Traffic Light Protocol) | Handling rules for who may see a product |
| **STIX** | Structured data model for CTI objects (indicators, malware, relationships, …) |
| **TAXII** | HTTPS API to publish and fetch STIX collections |

Do not mix them up: STIX is the content shape; TAXII is transport; TLP is the sharing policy.

### TLP 2.0 labels (awareness)

| Label | Sharing boundary |
|-------|------------------|
| TLP:RED | Only the individuals in that exchange — no further disclosure |
| TLP:AMBER | Organization and its clients, need-to-know |
| TLP:AMBER+STRICT | Organization only (not clients) when the source specifies it |
| TLP:GREEN | Community / peers and partners — not public channels |
| TLP:CLEAR | No limit on disclosure (publicly releasable subject to normal rules) |

TLP:WHITE is retired. Need wider sharing than the label allows? Ask the source — do not invent an exception.

## How CTI feeds red and blue work

| Team | Use |
|------|-----|
| Blue / SOC | Enrich alerts; hunt; tune detections |
| IR | Scope campaigns; prioritize containment |
| Purple / red (authorized) | Emulate known TTPs in a lab or scoped exercise |
| Builders | Prioritize patches and design mitigations for relevant techniques |

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] Distinguish data, information, and intelligence  
- [ ] Recite the intelligence lifecycle  
- [ ] Name four CTI levels with one example each  
- [ ] State what STIX, TAXII, and TLP each do  

## Next

- Practice depth → [Threat-Intelligence](../Threat-Intelligence/README.md)
- [Vulnerability management literacy](./20_Vulnerability_Management_Literacy.md)

## Further reading

- [FIRST — Traffic Light Protocol (TLP)](https://www.first.org/tlp/)
- [OASIS — STIX Version 2.1](https://docs.oasis-open.org/cti/stix/v2.1/os/stix-v2.1-os.html)
- [OASIS — TAXII Version 2.1](https://docs.oasis-open.org/cti/taxii/v2.1/os/taxii-v2.1-os.html)
- [NICCS — Cyber Threat Intelligence Fundamentals (catalog entry)](https://niccs.cisa.gov/training/catalog/leveleffect/cyber-threat-intelligence-fundamentals)
