# Metrics, purple, and improvement

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Defensive ops without feedback becomes alert theater. Measure what changes behavior, run **authorized** purple exercises that prove detections, and close lessons-learned tickets like production bugs. SOC (Security Operations Center) shape: [1](./1_Defensive_Ops_And_SOC_Shape.md). Detection engineering: [4](./4_Detection_Engineering_And_ATTCK.md).

## Concept

### Metrics that matter

| Metric | Honest use |
|--------|------------|
| **MTTD (Mean Time to Detect)** | From evidence of attack to awareness — needs good clocks and definitions |
| **MTTR (Mean Time to Respond / Recover)** | Define “respond” vs “fully recover” separately |
| Alert volume per analyst | Capacity and noise |
| True positive / false positive rates | Tuning quality |
| Coverage of priority ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) techniques with *tested* detections | Real readiness |
| % critical assets with required telemetry | Prerequisite for everything |
| Lesson-learned tickets closed | Whether post-incident reviews change systems |

Vanity: “millions of events ingested” without outcomes.

```text
Incident / purple / hunt
         │
         ▼
  lesson → ticket → detection or control change
         │
         ▼
  re-test → update metric / coverage map
```

### Purple in one paragraph

Agree techniques, disclose source ranges or lab targets, execute, watch whether alerts and tickets fire, fix gaps, re-test. Full exercise design: [15](./15_Tabletop_And_Purple_Exercises.md). Offense path: [Offensive](../Offensive/README.md). Recon-shaped detection cues: [Reconnaissance/11](../Reconnaissance/11_Detect_Harden_And_Continuous_Recon.md).

### Analyst health metrics (often ignored)

| Signal | Risk if ignored |
|--------|-----------------|
| After-hours pages per week | Burnout → missed Sev-1 |
| % alerts closed as FP (false positive) | Detection debt |
| Time in enrichment vs investigation | Automation opportunity ([13](./13_Automation_And_SOAR_Boundaries.md)) |
| Open lessons-learned age | Post-incident theater |

## Advanced

### Defining clocks

| Clock start | Clock end |
|-------------|-----------|
| First malicious action visible in logs | Analyst acknowledges alert |
| Alert created | Containment action completed |
| Incident declared | Service restored + monitoring |

Write definitions down or MTTD/MTTR debates never end.

### CSIRT service metrics

Service frameworks recommend measuring services you claim to offer (time to triage, time to notify constituents, exercise frequency). Measure only what your mandate includes — do not invent fake SLAs (service level agreements) for services you do not run.

### Post-incident review that works

| Do | Don’t |
|----|-------|
| Blameless facts + system fixes | Humiliate on-call |
| Three concrete tickets max as “must ship” | 40-item wishlist that dies |
| Re-test the missed detection | Assume the write-up fixed it |
| Update playbooks in the same sprint | Separate “docs later” forever |

### Continuous improvement backlog

Feed from: incidents, hunts ([5](./5_Threat_Hunting.md)), purple, ASM (Attack Surface Management) surprises, and vuln emergencies. Prioritize by crown-jewel exposure, not by who yelled last.

## Apply

1. Publish written definitions for MTTD and MTTR in your team.  
2. Run one disclosed lab purple test for a single ATT&CK technique; record detect yes/no.  
3. Close or kill every open “lessons learned” ticket older than 90 days.  
4. Update your technique coverage map after the test.

## Further reading

- [FIRST — CSIRT Services Framework](https://www.first.org/standards/frameworks/csirts/csirt_services_framework_v2.1)
- [FIRST — Metrics for the CSIRT Services Framework](https://www.first.org/global/sigs/metrics/)
- [NIST CSF 2.0](https://www.nist.gov/cyberframework)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [CISA — Cybersecurity Performance Goals](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals)
