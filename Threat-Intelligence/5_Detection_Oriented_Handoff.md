# Detection-oriented handoff

[← Threat-Intelligence](./README.md)

> **Progression:** concept → advanced → apply.

How CTI becomes hunts, detections, and blocks without drowning the SOC. Models: [2](./2_Intrusion_Analysis_Models.md). Products & TLP: [3](./3_Finished_Intelligence_Products.md). STIX packaging: [4](./4_STIX_And_TAXII_Practice.md). VM overlap: [Vulnerability-Management](../Vulnerability-Management/README.md).

## Concept

Finished intel that never changes detection, hunting, or response is a newsletter. The handoff is a **contract** between CTI and detection/IR engineering.

| CTI delivers | Detection / IR needs |
|--------------|----------------------|
| Context (PIR, campaign, ATT&CK, confidence) | Priority and expected FP profile |
| Indicators with **expiry** and TLP | What to block vs watch vs ignore |
| TTPs / procedures | Analytics that survive IOC churn |
| Suggested CoAs by Kill Chain phase | Owners and telemetry coverage |
| STIX Bundle / rule drafts | Idempotent ingest, test plan, rollback |

### Pyramid of Pain (David Bianco)

Bianco’s layers (bottom → top). Pain rises as you deny indicators the adversary cannot casually replace:

| Layer (bottom → top) | Examples (from Bianco) | Adversary pain if you detect/deny |
|----------------------|------------------------|-----------------------------------|
| Hash values | MD5/SHA of a specific file | Trivial — flip a bit / repack |
| IP addresses | C2 IP or netblock | Easy — rotate hosting / proxies |
| Domain names | FQDN or subdomain | Moderate — register/host anew |
| Network artifacts | Distinctive URI, User-Agent, C2-in-protocol quirks | Forces tool reconfigure |
| Host artifacts | Distinctive registry keys, paths, service names | Forces tool reconfigure |
| Tools | Specific backdoor/utility family (incl. many YARA/AV hits) | Costly retool / retrain |
| TTPs | Behaviors (e.g. pass-the-hash), not a named binary | Highest — change how they operate |

**Program implication:** spend CTI cycles climbing the pyramid. Hashes and IPs still have value as *fast* temporary controls — always with expiry and context — but TTP-level analytics are the resiliency play that matches Kill Chain CoA thinking ([2](./2_Intrusion_Analysis_Models.md)).

### Handoff ticket — minimum fields

| Field | Why |
|-------|-----|
| PIR / report link | Traceability |
| TLP | Redistribution / tooling visibility |
| ATT&CK IDs | Navigation and coverage maps |
| Kill Chain phases covered | CoA alignment |
| Observables | Type, value, STIX pattern if any |
| Action | Block / alert / hunt-only / informational |
| Confidence | Drives auto vs human-in-loop |
| **Expiry / revisit date** | Stops eternal FP sources |
| Telemetry needed | Which logs/sensors must see it |
| FP notes / allowlist hints | Save SOC time |
| Test evidence | What proved it fires (and on what) |
| Owner + backup | CTI and detection eng |

## Advanced

### From Diamond/ATT&CK to analytics

```text
Diamond capability + Victim telemetry
  → ATT&CK technique (procedure specifics)
    → Analytic idea (behavior, not only IOC)
      → Prototype query / rule
        → Test (TP sample + FP canaries)
          → Deploy with severity / expiry / owner
            → Sightings back into STIX ([4](./4_STIX_And_TAXII_Practice.md))
```

| Prefer | Avoid |
|--------|-------|
| Technique-scoped behavioral analytics | Single hash → critical severity forever |
| Layered CoAs (mail + EDR + DNS) | One brittle IDS sig as the only control |
| Hunt → detect → (maybe) block progression | Immediate org-wide block on untrusted feed |
| Sighting feedback to CTI | One-way firehose into SIEM |

### Malware / phishing triage as CTI collection

Day-to-day CTI often starts from a **sample or URL**, not a finished PDF. **Triage** (hashes, basic static, sandbox) is the first rung; deeper **reverse engineering** when PIRs need it is [6](./6_Malware_Reverse_Engineering_For_CTI.md).

```text
Suspect file/URL (policy-approved sandbox / detonation)
  → behaviors + dropped files + network callbacks
    → IOCs + ATT&CK procedures
      → Diamond infra pivots ([2](./2_Intrusion_Analysis_Models.md))
        → STIX + detection ticket
          → escalate to RE ([6](./6_Malware_Reverse_Engineering_For_CTI.md)) if packed / unclear / high-value PIR
```

| Do | Don’t |
|----|-------|
| Use approved sandbox/detonation; capture C2, mutexes, persistence | Detonate unknown malware on a production laptop |
| Extract *behaviors* for Pyramid-higher detections | Ship only a hash and call it done |
| Feed sightings back to the TIP | Ignore FP notes from SOC |

### TIP enrichment (where CTI work lives day to day)

A **Threat Intelligence Platform (TIP)** aggregates feeds, STIX/TAXII, and analyst objects so people can pivot and push to SIEM/SOAR. Real-world habit: **PIR-filter → enrich → stage → promote** ([4](./4_STIX_And_TAXII_Practice.md)). The TIP is not intelligence by itself — analysts still rate sources and write judgments.

### IR fusion awareness (F3EAD)

Some shops pair CTI with IR using **F3EAD** (Find, Fix, Finish, Exploit, Analyze, Disseminate): operators act on a target; CTI **exploits/analyzes** what was learned and **disseminates** so the next Find is smarter. You do not need the acronym to work — you need the loop: incident lessons → updated PIRs, STIX, and detections.

### YARA and YARA-X (awareness for CTI→malware handoff)

| Topic | Practice |
|-------|----------|
| **YARA** | Pattern language for files/memory — rules combine strings + conditions; staple for malware CTI handoff |
| **Rule hygiene** | Meta (author, PIR, TLP, ATT&CK, expiry); tight strings; `filesize` / PE checks to cut FPs |
| **Testing** | Goodware corpus + known-bad; track FP rate before fleet deploy |
| **YARA-X** | Next-gen YARA toolchain from VirusTotal (compatibility + performance focus) — track if your pipeline adopts it; validate rule dialect before migrating |
| **Not a silver bullet** | Packers, memory-only, and living-off-the-land procedures need complementary EDR/analytics |

CTI should attach **rule intent** (what family/behavior) and **safe scope** (sandbox vs fleet) — not only a gist link.

### Indicator lifecycle

```text
Propose → Enrich (context, TLP, confidence) → Approve action
  → Deploy (block/alert/hunt) → Monitor FP/TP
    → Extend · Retire · Retract
```

| State | Meaning |
|-------|---------|
| Propose | From report, AIS, ISAC, sandbox — not yet trusted |
| Enrich | Provenance, related STIX, ATT&CK, victim relevance |
| Deploy | In tooling with expiry |
| Retire | Expired or no longer relevant — remove from blocklists |
| Retract | Was wrong — remove *and* notify consumers (NIST 800-150) |

**Confidence × action matrix (illustrative policy):**

| Confidence | Typical action |
|------------|----------------|
| Low | Hunt / watchlist only |
| Moderate | Alert; block in high-fidelity channels (e.g. known-bad sinkhole) with short expiry |
| High + local sighting | Broader block / prevent; still schedule revisit |

### Feed and partner intake (NIST-aligned)

| Control | Why |
|---------|-----|
| PIR filter on subscriptions | Volume without relevance is cost |
| Source scorecard | Track false-positive rate by provider |
| Staging collection | TAXII → staging TIP → promote | 
| Marking enforcement | TLP:AMBER+STRICT must not land in wide SOAR playbooks |
| PII scrub | Especially email/file observables ([3](./3_Finished_Intelligence_Products.md)) |
| Dual track | Strategic/operational reports for humans; STIX for machines |

### Purple-team loop

| Step | CTI role | Detection role |
|------|----------|----------------|
| 1 | Select ATT&CK techniques from PIRs / campaigns | Confirm telemetry exists |
| 2 | Provide procedures / samples under TLP | Emulate *authorized* techniques |
| 3 | Observe gaps | Write/fix analytics |
| 4 | Update finished intel + STIX sightings | Close tickets with coverage notes |

Unauthorized “live” malware on production is out of scope — emulation stays inside approved purple/IR controls ([Threat-Modeling](../Threat-Modeling/README.md) for design-time abuse cases).

### Worked handoff snippet (illustrative)

```text
TLP:AMBER
PIR: Invoice-kit → finance VDI
ATT&CK: T1566.001, T1059.001
Action: (1) Mail gateway rule draft — alert 7d then prevent
         (2) EDR hunt — PowerShell encoded command from Outlook child process
         (3) DNS sinkhole domain X — expiry 30d
Confidence: Moderate (partner report + one internal sandbox detonation)
STIX: bundle--…
YARA: rule CTI_InvoiceLoader_… (sandbox queue only until FP < …)
FP notes: exclude CI build hosts …
Owner: detection@… / ctI@…
Revisit: 2026-11-30
```

## Apply

1. Take one public report; place each IOC/TTP on the Pyramid of Pain; mark where your current controls sit.  
2. Open a detection ticket using the minimum field table — fill every row (use placeholders only where data is truly unknown).  
3. Write a YARA rule *meta* section (no need for production strings) with PIR, TLP, ATT&CK, expiry, and safe scope.  
4. Define your org’s confidence × action policy in six lines; socialize with SOC lead.  
5. From a TAXII/AIS-style feed sample, list what you would **reject**, **stage**, and **auto-promote** — and why.  
6. Run a mini purple plan for one technique: telemetry check → emulate → detect → STIX sighting back to CTI.

### Self-check

- [ ] Explain the Pyramid of Pain and where TTPs sit  
- [ ] List the minimum handoff ticket fields  
- [ ] Describe indicator lifecycle including retract  
- [ ] Tie Kill Chain CoAs to layered mail/EDR/DNS actions  
- [ ] State YARA hygiene habits and why fleet deploy needs FP testing  
- [ ] Explain why TLP and PIR filters belong in automation paths  
- [ ] Outline malware/URL triage → IOC/TTP → detection ticket  
- [ ] State what a TIP is for (and what it is not)  
- [ ] Know when triage escalates to reverse engineering ([6](./6_Malware_Reverse_Engineering_For_CTI.md))  

## Next

- [Malware reverse engineering for CTI](./6_Malware_Reverse_Engineering_For_CTI.md)

## Further reading

- [Detect → Respond — Pyramid of Pain (Bianco)](https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html)
- [VirusTotal / YARA documentation](https://yara.readthedocs.io/)
- [VirusTotal — YARA-X](https://virustotal.github.io/yara-x/)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/)
- [NIST SP 800-150 — Guide to Cyber Threat Information Sharing](https://csrc.nist.gov/pubs/sp/800/150/final)
- [OASIS — STIX 2.1](https://docs.oasis-open.org/cti/stix/v2.1/stix-v2.1.html)
