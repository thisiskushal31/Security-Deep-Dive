# Blue, SOC, and DFIR labs

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Defensive practice trains **triage, forensics, and hunting** on authorized challenges or lab estates with logging built in. Program depth lives in [Defensive-Ops](../Defensive-Ops/README.md); here you pick places to apply it.

## Basic apply

### Platform drills (artifact → answer)

| Platform | Basic apply |
|----------|-------------|
| **CyberDefenders** | Pick a beginner blue CTF; download allowed artifacts only; answer questions from evidence |
| **LetsDefend** | Take a SOC simulation path; practice alert → investigate → close with rationale |
| **THM blue / SOC rooms** | Guided defender rooms when you want structure first ([4](./4_Guided_Platforms_THM_HTB_Academy.md)) |

```text
Alert / prompt ──▶ Collect artifacts (pcap, memory, logs)
                              │
                              ▼
                    Timeline ──▶ Hypothesis ──▶ Answer / containment note
```

### Tooling to install before deeper DFIR

| Job | Tooling home |
|-----|----------------|
| Packets | Wireshark / tcpdump |
| Memory | Volatility |
| Disk | Autopsy |
| Endpoint query | osquery / Velociraptor |
| SIEM practice | Wazuh |

Follow [13](./13_Labs_Tooling_Map.md). Practice only on challenge files or lab VMs you own.

### Note shape for blue sessions

```markdown
- Alert / scenario:
- Evidence list:
- Timeline (UTC):
- Root cause hypothesis:
- Containment / eradication you would recommend:
- Detection gap (what was missing?):
- Link: Defensive-Ops note #
```

## Advanced apply

### Stand up a defender-oriented AD lab

DetectionLab-class projects wire Windows logging, WEF, Sysmon, and often Splunk/osquery/Zeek for you. Use them to:

1. Generate benign + known-bad activity (authorized scripts / atomic tests you trust).  
2. Find the event in the SIEM / EVTX.  
3. Write a detection idea with false-positive notes — Defensive-Ops detection engineering.

Pair with offensive AD estates carefully: attack in GOAD-like labs ([8](./8_AD_And_Windows_Estates.md)), detect in a logging-rich twin when you can afford the hardware.

### Escalate realism

| Level | Drill |
|-------|-------|
| L1 | Single pcap / single host challenge |
| L2 | Multi-host timeline, ticket-style writeup |
| L3 | Purple: you attack a lab, then investigate your own trail ([11](./11_Purple_Detect_What_You_Did.md)) |

### Career framing without certification cosplay

CISA / NICE materials help you name the work role you are practicing (analyst, IR, hunt). Use that to choose the next lab set in [12](./12_Path_Map.md) — not as an excuse to touch live tenant logs without authorization.

## Next

- [AD and Windows estates](./8_AD_And_Windows_Estates.md)

## Further reading

- [CyberDefenders](https://cyberdefenders.org/)
- [LetsDefend](https://letsdefend.io/)
- [DetectionLab](https://github.com/clong/DetectionLab) · [DetectionLab docs](https://dl-docs.netlify.app/)
- [Defensive-Ops](../Defensive-Ops/README.md)
- [CISA — training & exercises](https://www.cisa.gov/topics/cybersecurity-best-practices/cybersecurity-training-exercises)
