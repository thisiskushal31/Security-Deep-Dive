# STIX and TAXII practice

[← Threat-Intelligence](./README.md)

> **Progression:** concept → advanced → apply.

Machine-readable CTI packaging and transport so humans and tools share the same objects. Products: [3](./3_Finished_Intelligence_Products.md). Detection handoff: [5](./5_Detection_Oriented_Handoff.md). Literacy: [Foundations/19](../Foundations/19_Threat_Intelligence_Literacy.md).

## Concept

| Standard | Role |
|----------|------|
| **STIX 2.1** | Language — JSON objects for threat intel (who/what/when/how, relationships, observables) |
| **TAXII 2.1** | Transport — HTTPS API to **publish** and **collect** STIX over Collections in an API Root |

Without STIX, every vendor invents a CSV. Without TAXII (or equivalent), every partner invents a portal scrape. Neither replaces analytic quality ([1](./1_Analytic_Tradecraft.md)) — they package it.

```text
Analyst judgment + evidence
  → STIX Domain Objects + Relationships (+ Cyber Observables)
    → Bundle
      → TAXII Collection (or file / AIS / MISP export)
        → Consumer TIP / SIEM / SOAR / detection backlog ([5](./5_Detection_Oriented_Handoff.md))
```

### STIX 2.1 building blocks

| Category | Examples | Role |
|----------|----------|------|
| **SDO** (Domain Objects) | `indicator`, `malware`, `attack-pattern`, `threat-actor`, `intrusion-set`, `campaign`, `identity`, `report`, `vulnerability`, `tool`, `course-of-action`, `observed-data`, `opinion`, `grouping`, … | Semantic CTI statements |
| **SCO** (Cyber Observables) | `ipv4-addr`, `domain-name`, `url`, `file`, `email-message`, `process`, `windows-registry-key`, … | Technical observables (often embedded or linked via observed-data) |
| **SRO** (Relationship Objects) | `relationship`, `sighting` | Connect SDOs (“malware **indicates** indicator”; “identity **sighting-of** indicator”) |
| **Bundle** | `bundle` | Ship a set of objects together (not a persistent “database”) |
| **Meta / marking** | `marking-definition` (incl. TLP), extension objects | Handling and custom extensions |

**IDs:** STIX IDs look like `indicator--<UUIDv4>`. Stable IDs enable updates and cross-references; minting a new ID for the “same” indicator without a relationship creates duplicates in every TIP.

### Minimal indicator object (illustrative shape)

```json
{
  "type": "indicator",
  "spec_version": "2.1",
  "id": "indicator--a1b2c3d4-1111-2222-3333-444455556666",
  "created": "2026-10-30T12:00:00.000Z",
  "modified": "2026-10-30T12:00:00.000Z",
  "name": "Malicious domain example.com",
  "description": "C2 domain observed in invoice-themed campaign; moderate confidence.",
  "indicator_types": ["malicious-activity"],
  "pattern": "[domain-name:value = 'example.com']",
  "pattern_type": "stix",
  "valid_from": "2026-10-30T12:00:00.000Z",
  "valid_until": "2026-11-30T12:00:00.000Z",
  "object_marking_refs": [
    "marking-definition--34098fce-860f-48ae-8e50-ebd3cc5e41da"
  ]
}
```

The `object_marking_refs` UUID above is the **OASIS STIX 2.1 canonical `TLP:GREEN`** id. STIX 2.1 requires these four standard TLP marking-definition instances (do not mint your own):

| FIRST TLP 2.0 | STIX 2.1 `name` | Canonical `id` |
|---------------|-----------------|----------------|
| CLEAR | `TLP:WHITE` | `marking-definition--613f2e26-407d-48c7-9eca-b8e91df99dc9` |
| GREEN | `TLP:GREEN` | `marking-definition--34098fce-860f-48ae-8e50-ebd3cc5e41da` |
| AMBER | `TLP:AMBER` | `marking-definition--f88d31f6-486f-44da-b317-01333bde0b82` |
| RED | `TLP:RED` | `marking-definition--5e57c739-391a-4eb3-b6be-7d15ca92d5ed` |

(STIX 2.1 still says WHITE for unrestricted; FIRST TLP 2.0 renamed that label CLEAR. AMBER+STRICT is a FIRST sharing restriction — not a separate STIX TLP marking in this table.)

Patterns use STIX patterning (`[file:hashes.'SHA-256' = '…']`, etc.). Consumers that only ingest “free text IOCs” lose validity windows, markings, and relationships — push for real STIX where tooling allows.

### TAXII 2.1 mental model

| Concept | Meaning |
|---------|---------|
| **Server** | Hosts one or more API Roots |
| **API Root** | Logical grouping of Collections + discovery |
| **Collection** | Named set of STIX objects you can **add to** (publish) or **get from** (collect) |
| **Envelope** | TAXII wrapper for objects returned/submitted |
| **Auth** | Per deployment (API keys, mTLS, OAuth, …) — plan before volume |

Typical client flow: discover API roots → list collections → `GET` objects (with time filters) → optionally `POST` new objects if permitted.

## Advanced

### Object graph habits that prevent TIP sludge

| Habit | Why |
|-------|-----|
| Prefer relationships over stuffing everything into `description` | Machines can graph; analysts can pivot |
| Link `attack-pattern` (ATT&CK) to malware/indicators | Ties [2](./2_Intrusion_Analysis_Models.md) to detections |
| Use `sighting` when *you* observed the indicator | Feedback improves producer confidence |
| Set `valid_from` / `valid_until` | Aligns with indicator expiry ([5](./5_Detection_Oriented_Handoff.md)) |
| Carry TLP via `marking-definition` | Enforce [3](./3_Finished_Intelligence_Products.md) in the machine layer |
| Version via `modified` + same `id` | Update in place; do not fork IDs casually |
| Revoke / deprecate bad objects | NIST 800-150 retract path — do not leave immortal FPs |

### Common SDOs for CTI teams (when to use which)

| SDO | Use when |
|-----|----------|
| `indicator` | Detectable pattern you want enforced or hunted |
| `observed-data` | Raw observation snapshot (with SCOs) without asserting malice alone |
| `malware` / `tool` | Family or utility assertions |
| `attack-pattern` | ATT&CK technique mapping |
| `campaign` / `intrusion-set` / `threat-actor` | Clustering judgments — higher bar; apply ACH ([1](./1_Analytic_Tradecraft.md)) |
| `identity` | Org/sector victim or reporter |
| `report` | Finished narrative product packaging |
| `course-of-action` | Recommended mitigations (map to Kill Chain CoAs) |
| `vulnerability` | CVE-linked context (handoff to [VM](../Vulnerability-Management/README.md)) |

### CISA AIS and community channels (awareness)

| Channel | Notes |
|---------|--------|
| **CISA AIS 2.0** | U.S. Automated Indicator Sharing — STIX/TAXII-oriented government↔industry indicator exchange; requires enrollment and adherence to AIS terms/markings |
| **ISACs / ISAOs** | Sector collections often expose TAXII or portal + STIX download |
| **MISP** | Open threat-sharing platform — events/attributes; interop paths to STIX exist; useful when partners are MISP-native |
| **Vendor TIP** | Commercial platforms ingest STIX/TAXII; still apply PIR filters so automation does not flood SIEM |

Automation without **PIR filters**, **TLP enforcement**, and **expiry** creates alert debt. Scope TAXII subscriptions like you scope log sources.

### Interoperability pitfalls

| Pitfall | Mitigation |
|---------|------------|
| STIX 1.x XML vs 2.x JSON | Prefer 2.1 end-to-end; translate at the edge if needed |
| Custom properties without extensions | Use STIX extension mechanism for durable custom fields |
| Pattern dialect mismatches | Document `pattern_type`; test in consumer before fleet-wide block |
| Bundle ≠ Collection | Bundle is a file/package; Collection is TAXII persistence |
| Over-sharing PII in SCOs | Email/file SCOs can contain personal data — redact per policy ([3](./3_Finished_Intelligence_Products.md)) |
| Trust on day one | TAXII credentials ≠ vetted partner — contracts/MOUs first (NIST 800-150) |

### Tiny relationship example (illustrative)

```text
threat-actor--…  ─uses→  malware--…
malware--…       ─indicates→  indicator--…
indicator--…     ←sighting─  identity--(your SOC)
attack-pattern--(T1566)  ←uses─  malware--…
```

Encode those as `relationship` / `sighting` SROs inside the same Bundle so a TIP can draw the graph.

## Apply

1. Hand-write (or generate) a STIX 2.1 Bundle with: one `indicator`, one `malware`, one `attack-pattern`, two `relationship` objects, and a TLP marking.  
2. Add `valid_until` and a one-line `description` that a detection engineer can use without the PDF report.  
3. Map your Bundle fields to the finished-product outline in [3](./3_Finished_Intelligence_Products.md) — what still lives only in prose?  
4. Draft the TAXII operational checklist: API Root URL, Collection ID, auth method, poll interval, TLP policy, who may publish.  
5. Simulate a false positive: write the updated/revoked object changes (same `id`, new `modified`, confidence or labels).  
6. Compare one MISP event export and one STIX Bundle covering the same intel — list three fields that round-trip poorly.

### Self-check

- [ ] Explain STIX vs TAXII in one sentence each  
- [ ] Define SDO, SCO, SRO, and Bundle  
- [ ] Read a STIX pattern for a domain or file hash  
- [ ] Describe TAXII Collection publish vs collect  
- [ ] List three object-graph habits (relationships, validity, markings)  
- [ ] State why AIS/MISP/TIP still need PIR and TLP filters  

## Next

- [Detection-oriented handoff](./5_Detection_Oriented_Handoff.md)

## Further reading

- [OASIS — STIX 2.1 specification](https://docs.oasis-open.org/cti/stix/v2.1/stix-v2.1.html)
- [OASIS — TAXII 2.1 specification](https://docs.oasis-open.org/cti/taxii/v2.1/taxii-v2.1.html)
- [OASIS CTI TC (STIX/TAXII)](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=cti)
- [CISA — Automated Indicator Sharing (AIS)](https://www.cisa.gov/topics/cyber-threats-and-advisories/information-sharing/automated-indicator-sharing-ais)
- [MISP Project](https://www.misp-project.org/)
- [NIST SP 800-150 — Guide to Cyber Threat Information Sharing](https://csrc.nist.gov/pubs/sp/800/150/final)
