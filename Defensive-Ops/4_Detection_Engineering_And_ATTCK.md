# Detection engineering and ATT&CK

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Detection engineering treats detections as **products**: versioned, tested, owned, and mapped to adversary behavior. ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) is the common language for “what behavior are we covering?” D3FEND complements with defensive countermeasure vocabulary. Platform context: [3](./3_SIEM_And_Detection_Platforms.md).

## Concept

### From technique to telemetry to alert

```text
ATT&CK technique (behavior)
        │
        ▼
Required data (process, auth, DNS, …)
        │
        ▼
Detection logic (rule / model / hunt query)
        │
        ▼
Alert → triage runbook → IR if confirmed
```

| Better | Worse |
|--------|-------|
| High-fidelity + owner + runbook | Alert spam nobody acknowledges |
| Tested with purple / unit fixtures | Rules never validated |
| Tuned suppressions with expiry | Permanent mute of real activity |
| Mapped to techniques *and* assets | Poster coverage with empty logs |

### Coverage is not a percentage of the matrix

Ask:

1. Which techniques matter for **our** crown jewels?  
2. Which of those have telemetry today?  
3. Which have a tested detection?  
4. Which have a response path?

A short list with depth beats painting the whole ATT&CK matrix green.

## Advanced

### Fidelity and cost

| Dimension | Trade-off |
|-----------|-----------|
| True positive rate | Misses vs noise |
| Time-to-investigate | Enrichment vs raw events |
| Attacker cost to evade | Brittle IOCs (indicators of compromise) vs behavioral |
| Analyst cost | Volume the team can actually work |

Prefer detections that survive minor attacker variation (living-off-the-land patterns, unusual auth sequences) over single hash matches alone — hashes still help for known malware families when fresh.

### D3FEND as the defensive mirror

When you pick a control or detection theme, name **how** you counter a technique (detect, harden, isolate, deceive, …). That keeps engineering conversations concrete: “we detect process injection via X telemetry” rather than “we have EDR (Endpoint Detection and Response).”

### Testing detections (authorized)

| Method | Use |
|--------|-----|
| Atomic / lab replay of benign-looking technique | Prove the rule fires |
| Purple exercise with disclosed scope | Prove SOC (Security Operations Center) path works |
| Historical replay on past incidents | Reduce regressions |
| Peer review of rule logic | Catch brittle field assumptions |

Never validate detections by attacking systems outside authorization — [Foundations/3](../Foundations/3_Ethics_Authorization_And_Lab_Rules.md).

### Detection lifecycle (ship → retire)

| Stage | Work |
|-------|------|
| **Request** | Technique / risk / incident lesson |
| **Design** | Data, logic, fidelity target, runbook |
| **Implement** | Rule / model in reviewable form |
| **Validate** | Lab replay / purple ([15](./15_Tabletop_And_Purple_Exercises.md)) |
| **Operate** | Tune; suppressions with expiry |
| **Retire** | Dead techniques, broken fields, replaced by better detection |

Unowned rules rot. Every detection needs a named engineer and a review date.

### Detection-as-code habits

Keep rules in reviewable form (repo, PR (pull request), CI (continuous integration) syntax checks). Portable rule formats help move logic across platforms; product-specific languages still need the same ownership model. Pattern matching for files/memory often uses YARA in the lab — install: [Tooling YARA](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/YARA). Do not fork a second tool tree here.

### Email, identity, and “middle” detections

Crown-jewel paths are not only endpoint malware:

| Surface | Example detection themes |
|---------|--------------------------|
| Mail | Impossible travel + mail rule; mass forward creates |
| Identity | MFA (multi-factor authentication) fatigue; token theft patterns |
| DNS | Beacon-like query timing; newly seen domains from servers |
| CI (continuous integration) | Unexpected workflow file changes; secret use from odd runners |

## Apply

1. Pick three ATT&CK techniques relevant to your stack; list required data sources.  
2. For each, mark: telemetry present? detection exists? tested?  
3. Write a one-page runbook for your noisiest high-severity alert.  
4. Schedule one authorized lab test of a single detection this month.  
5. Retire or re-home one detection that has no owner.

## Further reading

- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE ATT&CK — Data Sources](https://attack.mitre.org/datasources/)
- [MITRE D3FEND](https://d3fend.mitre.org/)
- [NIST CSF 2.0 — Detect](https://www.nist.gov/cyberframework/detect)
