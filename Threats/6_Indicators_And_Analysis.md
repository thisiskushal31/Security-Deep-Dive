# Indicators and analysis

[← Threats](./README.md)

> **Progression:** concept → advanced → apply.

How you **notice** malicious activity after notes [1](./1_Actors_And_Motivations.md)–[5](./5_Attack_Classes.md) taught you what to expect. Indicators suggest investigation; they are not automatic proof of an incident. Ops depth: [Defensive-Ops](../Defensive-Ops/README.md) · [NIST SP 800-61r3](https://csrc.nist.gov/pubs/sp/800/61/r3/final).

## Concept

An **indicator** (including indicators of compromise / IoCs) is an observable clue — technical artifact or behavioral anomaly — that *may* mean malicious activity.

**Adverse event analysis** studies monitoring data to find possible attacks and compromises and to decide when an **incident** has occurred so response can start. Volumes are high: filter and correlate before humans review everything. Anomalies can be benign or malicious. CTI improves early detection.

```text
Raw telemetry → filter/correlate → indicators/anomalies
        → analyze (multi-source) → incident? → respond
```

Find incidents **earlier** in the attack life cycle when possible — later signs are clearer but impact is often larger.

## Advanced

### Generic indicators (Security+ list)

| Indicator | What it may mean | Caution |
|-----------|------------------|---------|
| **Account lockouts** (burst) | Password attack (brute force or spray) or misconfigured automation | Check whether many accounts (spray) or one account (brute) |
| **Concurrent session usage** | Account takeover; shared credentials; token theft | Sales/events can raise legitimate concurrency |
| **Impossible travel** | Same identity appears in distant geos too quickly | VPNs, proxies, and mobile networks can create false geo-jumps — correlate device/session signals |
| **Blocked content** | Filters stopped a delivery attempt | Useful hunt seed; not proof of success |
| **Resource consumption** | Crypto-mine, worm, DDoS participant, runaway job | Patch windows and business spikes also raise CPU/network |
| **Resource inaccessibility** | Ransomware, wipe, DoS, outage | Confirm before declaring ransomware |
| **Out-of-cycle logging** | Unexpected verbosity or logging outside change windows | May be debug left on — still investigate |
| **Missing logs** | Tampering or logging failure | Gaps during suspicious windows are high priority |
| **Published / documented** | Matches public IoC lists, leak sites, or advisories | Validate against your environment; avoid alert fatigue |

### Mapping indicators → attack classes (examples)

| Clue pattern | Suspect class |
|--------------|---------------|
| Mass file rename + ransom note | Ransomware ([4](./4_Malware.md)) |
| Fast lateral SMB-style scanning / sequential infections | Worm |
| Periodic low-volume outbound beacons | Trojan / C2-style implant |
| Host view disagrees with external scan; hidden drivers | Rootkit |
| Dormant until date/event | Logic bomb |
| Few passwords × many accounts, slow | Password spraying ([5](./5_Attack_Classes.md)) |
| Many passwords × one account, fast | Brute force |
| TLS/protocol suddenly weaker | Downgrade |
| Two inputs, same hash | Collision |
| DNS answers wrong / suspicious TTL shifts | DNS attack |
| Rogue AP matching SSID | Wireless / evil twin |
| Same session token from distant IPs | Credential replay |
| URL with `../` sequences toward sensitive paths | Directory traversal |
| Log gap for a critical window | Missing logs / tampering |

These are **recognition patterns** for literacy — not a substitute for investigation playbooks.

### Analysis habits (NIST-aligned)

| Habit | Why |
|-------|-----|
| Correlate multiple sources | One log rarely tells the whole story — use SIEM-class correlation |
| Use CTI in analysis tools | Better characterization of actors/methods/IoCs |
| Manual review where automation cannot | Some tech cannot be monitored only by SIEM |
| Declare incidents by criteria | Not every anomaly is an incident |
| Prefer early detection | Smaller blast radius |

## Apply

1. List indicators present in the scenario.  
2. Hypothesize an attack class ([4](./4_Malware.md) · [5](./5_Attack_Classes.md)).  
3. Seek corroborating sources before declaring an incident.  
4. Contain appropriately; preserve evidence when IR requires it.  
5. Afterward, improve detections and mitigations ([7](./7_Mitigations.md)).

### Self-check

- [ ] Explain why an indicator is not automatically an incident  
- [ ] Contrast spraying vs brute force using lockout patterns  
- [ ] Give two benign explanations for “impossible travel” or resource spikes  
- [ ] Name eight generic indicators from the Security+ list  
- [ ] State why multi-source correlation matters  

### Real cases (these already happened)

Short examples from official public reports so you know this is not theory. Links have the full story — this is **not** a how-to.

| Pattern | Why analysis habits matter | Where to read more |
|---------|----------------------------|--------|
| **Ransomware + data theft** | Mass encryption *and* exfil — look for both resource inaccessibility and unusual outbound/copy activity | NIST SP 800-61r3 incident examples · [4](./4_Malware.md) |
| **Phishing → account fraud** | Early indicators: user reports, impossible travel, concurrent sessions, anomalous mail rules — before wire transfer completes | IC3 BEC / phishing volume · [2](./2_Vectors_And_Surfaces.md) |
| **Missing or tampered logs** | Adversaries hide; logging gaps during a suspicious window are themselves indicators | Domain 2 indicator list above |
| **KEV / public IoCs** | “Published/documented” indicators speed hunting — still validate in *your* environment | [CISA KEV](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) · [CISA advisories](https://www.cisa.gov/news-events/cybersecurity-advisories) |

AI-era indicators (synthetic media, anomalous model-query patterns, odd agent tool use, poisoned retrieval): [8](./8_AI_Era_Threats.md).

## Next

- [Mitigations](./7_Mitigations.md)

## Further reading

- [NIST SP 800-61r3 — Incident Response Recommendations (CSF 2.0 Community Profile)](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [CISA — Cybersecurity Advisories](https://www.cisa.gov/news-events/cybersecurity-advisories)
- [CompTIA Security+ SY0-701 Exam Objectives](https://comptiacdn.azureedge.net/webcontent/docs/default-source/exam-objectives/comptia-security-sy0-701-exam-objectives-(6-0).pdf) (Domain 2.4 indicators)
