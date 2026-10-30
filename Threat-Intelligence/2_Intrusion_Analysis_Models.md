# Intrusion analysis models

[← Threat-Intelligence](./README.md)

> **Progression:** concept → advanced → apply.

How analysts structure *what happened* so collection and defense have a map. Analytic method: [1](./1_Analytic_Tradecraft.md). Products: [3](./3_Finished_Intelligence_Products.md). Detection handoff: [5](./5_Detection_Oriented_Handoff.md).

## Concept

Three complementary models. Use them together — none replaces the others.

| Model | Core idea | Best for |
|-------|-----------|----------|
| **Intrusion Kill Chain** (Hutchins, Cloppert, Amin — Lockheed Martin) | Seven phases an intrusion must complete | Mapping CoAs; forcing reuse of adversary infrastructure to cost them |
| **Diamond Model** (Caltagirone, Pendergast, Betz — DoD monograph) | Adversary · Capability · Infrastructure · Victim + meta-features | Pivoting, clustering, campaign reconstruction |
| **MITRE ATT&CK** | Tactics → techniques → procedures (enterprise / mobile / ICS / …) | Detection coverage, Navigator heat maps, purple-team scope |

### Kill Chain — seven phases (definitions)

Lockheed Martin’s model adapts military F2T2EA thinking to network defense. An intrusion must breach a trusted boundary, establish presence, then act toward objectives. **Any broken link can interrupt the chain.**

| Phase | Definition (paper) |
|-------|---------------------|
| **1. Reconnaissance** | Research, identify, select targets (e.g. crawl sites, conferences, mailing lists for emails, relationships, tech stack) |
| **2. Weaponization** | Couple RAT/exploit into a deliverable (weaponizer); often PDF/Office as the weaponized file |
| **3. Delivery** | Transmit weapon to target — historically email attachments, websites, USB |
| **4. Exploitation** | Trigger code — vuln in app/OS, user social engineering, or auto-execute features |
| **5. Installation** | Install RAT/backdoor for persistence |
| **6. C2** | Outbound beacon to controller; “hands on keyboard” for interactive APT |
| **7. Actions on Objectives** | Exfil, integrity/availability attacks, or use host as hop for lateral movement |

**Insight that changes defense:** a “zero-day” is often only a change in *exploitation*. If delivery infrastructure, weaponization tooling, or C2 is reused, layered CoAs on *other* phases still stop the campaign. Completeness of CoAs across phases ≈ **resiliency**.

### Diamond Model — four core features

| Vertex | Meaning |
|--------|---------|
| **Adversary** | Operator / organization behind the intrusion |
| **Capability** | Tools, techniques, tradecraft used |
| **Infrastructure** | Physical and logical structures the capability rides on (hosts, domains, emails, …) |
| **Victim** | Target person, org, asset, or network |

Edges are **relationships** (e.g. adversary *uses* capability *against* victim *via* infrastructure). Every event is a diamond; campaigns are linked diamonds over time.

```text
                    Adversary
                   /          \
                  /            \
         Capability -------- Infrastructure
                  \            /
                   \          /
                      Victim
```

**Meta-features** enrich events: timestamp, phase, result, direction, methodology, resources — so you can thread “same actor, different day” without collapsing distinct events.

### ATT&CK — tactics vs techniques vs procedures

| Layer | Meaning |
|-------|---------|
| **Tactic** | Adversary *goal* in a phase (e.g. Persistence, Lateral Movement) |
| **Technique / sub-technique** | *How* (e.g. T1059.001 PowerShell) |
| **Procedure** | Specific implementation observed in the wild |

Map Diamond *capability* and Kill Chain *phase* into ATT&CK IDs so detection and purple work share a vocabulary with [VM](../Vulnerability-Management/README.md) and [Threat-Modeling](../Threat-Modeling/README.md).

## Advanced

### Courses of Action matrix (Kill Chain × DoD IO verbs)

The Kill Chain becomes **actionable intelligence** when defenders align enterprise capabilities to adversary process. Lockheed maps each phase to: **Detect · Deny · Disrupt · Degrade · Deceive · Destroy** (from DoD information operations doctrine).

Illustrative CoA cells (from the paper’s Table 1 — examples, not a complete catalog):

| Phase | Detect | Deny | Disrupt / Degrade / Deceive (examples) |
|-------|--------|------|----------------------------------------|
| Reconnaissance | Web analytics | Firewall ACL | — |
| Weaponization | NIDS | NIPS | — |
| Delivery | Vigilant user | Proxy filter | In-line AV · queuing |
| Exploitation | HIDS | Patch | DEP |
| Installation | HIDS | chroot jail | AV |
| C2 | NIDS | Firewall ACL | NIPS · DNS tarpit / redirect |
| Actions on Objectives | Audit log | — | QoS · honeypot |

**Metrics idea:** after each intrusion attempt, mark which phases had *detection*, which had *mitigation*, which were empty. Reuse by the adversary of tools/infra while you fill CoA gaps is how defenders raise adversary cost. Late-phase detection (only after exploit) is the classic disadvantaged posture — reconstruct *earlier* phases so next month’s attempt dies at delivery or C2.

**Unsuccessful intrusions matter:** blocked phishing that reused a known indicator may still hide a new exploit or backdoor in the payload. Synthesize the rest of the chain so future different delivery paths do not blindside you.

### Diamond — pivots, threads, groups, activity threads

| Analytic move | Meaning |
|---------------|---------|
| **Pivot** | From one feature, expand to related features (victim → shared malware → other victims; domain → hosting ASN → sibling domains) |
| **Activity thread** | Ordered chain of related events reconstructing a course of action |
| **Activity group** | Cluster of events/threads hypothesized as same adversary/capability/infra pattern |
| **Knowledge base** | Accumulated diamonds + relationships that make future pivots faster |

**Pivot discipline:** write the *hypothesis* for each pivot (“same registrant ⇒ same operator?”) and apply ACH ([1](./1_Analytic_Tradecraft.md)) when clusters collide. Shared commodity malware ≠ shared adversary.

**Meta-feature uses:** time windows for correlation; phase tags to align with Kill Chain CoAs; result (success/fail) so failed attempts still enrich the KB.

### Infrastructure pivoting (daily CTI skill)

Real CTI investigation is largely **legal, passive enrichment** from one observable to related infrastructure — then testing whether the link is diagnostic. Common pivot types (tool names change; the *questions* do not):

| From | Pivot via | Hypothesis to test |
|------|-----------|---------------------|
| Domain / FQDN | Passive DNS history, related hostnames | Same actor infra vs shared CDN/parking |
| IP | Co-hosted domains, ASN/netblock, historical resolutions | Dedicated C2 vs noisy shared hosting |
| TLS certificate | Same cert / same subject / same public key on other hosts | Related infrastructure cluster |
| File hash | Malware DB / sandbox → C2 domains, mutexes, similar samples | Family/tooling cluster |
| Email | Headers, sending infra, kit URL, attachment hash | Campaign vs commodity spray |
| URL / URI path | Unique path or UA string across victims | Same toolkit / same operator |

```text
Observable → enrich (pDNS / WHOIS / cert / sandbox)
  → candidate related observables
    → Diamond edges + ACH (“same adversary?”)
      → keep / discard → STIX relationships ([4](./4_STIX_And_TAXII_Practice.md))
```

**Pivoting hygiene:** stay inside policy and law; prefer **passive** sources for day-to-day CTI; do not treat “same registrar” or “same Cloudflare IP” as attribution; log each hop so peers can reproduce the graph. Active scanning of third-party assets may be out of policy — ask before you probe.

### ATT&CK as a TI knowledge layer

| Use | Practice |
|-----|----------|
| Tag finished intel | Every TTP claim gets technique IDs |
| Navigator | Heat-map *observed* vs *covered by detection* vs *accepted risk* |
| Detection engineering | Prefer technique-level analytics over brittle single IOCs ([5](./5_Detection_Oriented_Handoff.md)) |
| Purple / adversary emulation | Scope authorized tests to techniques relevant to *your* PIRs |
| Threat modeling | Feed STRIDE/DFD workshops with ATT&CK-relevant abuse paths ([Threat-Modeling/6](../Threat-Modeling/6_AI_And_Agentic_Threat_Modeling.md) for AI systems) |

**TI → ATT&CK mapping habit:**

```text
Raw report / incident
  → extract procedures (what was done)
    → map to technique IDs (ATT&CK)
      → place on Kill Chain phase (when in the intrusion)
        → Diamond vertices (who/what/infra/victim)
          → CoA + detection tickets
```

### Cross-walk (one campaign vignette)

| Kill Chain | Diamond focus | Example ATT&CK |
|------------|---------------|----------------|
| Recon | Adversary ↔ Victim research | T1598 Phishing for Information |
| Weaponization | Capability packaging | — (pre-delivery) |
| Delivery | Infra → Victim | T1566 Phishing |
| Exploitation | Capability on Victim | T1203 Exploitation for Client Execution |
| Installation | Capability persistence | T1547 Boot or Logon Autostart |
| C2 | Infra channel | T1071 Application Layer Protocol |
| Actions | Objectives on Victim | T1048 Exfiltration Over Alternative Protocol |

### Models vs Pyramid of Pain

Kill Chain / Diamond / ATT&CK tell you *structure*. The Pyramid of Pain ([5](./5_Detection_Oriented_Handoff.md)) tells you *how much it hurts the adversary* when you detect/deny each layer. Prefer TTP and tool detections that survive IOC churn; still use hash/IP blocks as *tactical* speed bumps with expiry.

## Apply

1. Reconstruct one public intrusion across all seven Kill Chain phases; mark which phases you *inferred* vs *observed*.  
2. Build a CoA matrix for that campaign: at least one Detect and one Deny/Disrupt cell per phase you care about.  
3. Draw diamonds for two related events; list three pivots (include at least one of: pDNS, TLS cert, hash→C2) and one alternative (benign) explanation for the strongest pivot.  
4. Map five observed procedures to ATT&CK technique IDs; paint a mini Navigator (Observed / Detected / Gap).  
5. Take one “blocked” phishing email from your org (or a public sample): synthesize what *would* have happened in later Kill Chain phases if delivery had succeeded.  
6. Write one paragraph that uses Kill Chain + Diamond + ATT&CK without contradicting yourself.

### Self-check

- [ ] Name and define all seven Kill Chain phases  
- [ ] Explain why zero-day exploitation alone need not defeat layered CoAs  
- [ ] Name Diamond’s four vertices and one meta-feature use  
- [ ] Define pivot, activity thread, and activity group  
- [ ] Name three infrastructure pivot types and the hypothesis each tests  
- [ ] Distinguish tactic, technique, and procedure  
- [ ] Sketch TI → ATT&CK → CoA/detection flow  

## Next

- [Finished intelligence products](./3_Finished_Intelligence_Products.md)

## Further reading

- [Lockheed Martin — Intelligence-Driven Computer Network Defense (Kill Chain PDF)](https://www.lockheedmartin.com/content/dam/lockheed-martin/rms/documents/cyber/LM-White-Paper-Intel-Driven-Defense.pdf)
- [Diamond Model of Intrusion Analysis (PDF)](https://www.activeresponse.org/wp-content/uploads/2013/07/diamond.pdf)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/)
- [MITRE — Getting Started with ATT&CK](https://attack.mitre.org/resources/getting-started/)
