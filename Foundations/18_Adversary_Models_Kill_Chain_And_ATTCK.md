# Adversary models: Kill Chain, Diamond, ATT&CK, Pyramid of Pain

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Offense, defense, and intelligence courses all assume you can talk about **how intrusions unfold** and **how to describe adversary behavior**. This note is that shared literacy. Actor catalogs and vectors: [Threats](../Threats/README.md). Framework nesting: [GRC](../GRC/README.md). Pentest phases: [Offensive](../Offensive/README.md). SOC use: [Defensive-Ops](../Defensive-Ops/README.md).

## Concept

## Why these models exist

| Need | Model family |
|------|----------------|
| Sequence of an intrusion | Cyber Kill Chain |
| Pivot analysis of one event | Diamond Model |
| Shared language for techniques | MITRE ATT&CK |
| Which detections hurt attackers most | Pyramid of Pain |

No single model replaces the others. Analysts often use more than one on the same case.

## Cyber Kill Chain (sequence literacy)

An intrusion is often taught as a staged sequence. A widely used seven-stage framing is:

```text
1 Reconnaissance
2 Weaponization
3 Delivery
4 Exploitation
5 Installation
6 Command and Control (C2)
7 Actions on Objectives
```

| Stage | Defender question |
|-------|-------------------|
| Reconnaissance | What can they learn about us without touching us hard? |
| Weaponization | What packages or docs are they preparing? |
| Delivery | How does the payload arrive (email, web, removable media)? |
| Exploitation | Which vulnerability or weakness is triggered? |
| Installation | How do they persist? |
| C2 | How do they talk back to control? |
| Actions on objectives | What is the real goal (theft, ransomware, fraud)? |

Linear models help teaching and reporting. Real campaigns can skip, repeat, or parallelize stages — still name the stage you are disrupting.

| Stage | Example disruption (literacy) |
|-------|-------------------------------|
| Delivery | Block known-bad attachments; user reporting |
| Exploitation | Patch; exploit mitigation |
| Installation | Application allow lists; EDR on persistence |
| C2 | Egress control; DNS / proxy monitoring |
| Actions on objectives | Least privilege; DLP; immutable backups |

## Advanced

## Diamond Model (event literacy)

An intrusion **event** can be described with four core features:

```text
        Adversary
       /          \
Capability ---- Infrastructure
       \          /
         Victim
```

| Vertex | Meaning |
|--------|---------|
| Adversary | Who is taking the step |
| Capability | What tool, malware, or technique they use |
| Infrastructure | What they use to deliver or control (domains, servers, accounts) |
| Victim | Who or what is targeted |

Edges support **pivoting**: from a domain (infrastructure) you may find more victims or related capabilities. Events link into activity threads over time.

## MITRE ATT&CK (behavior literacy)

ATT&CK is a knowledge base of adversary **tactics** (goals) and **techniques** (how). Use it to:

- Label detections and incidents with a shared ID  
- Find coverage gaps (“no visibility for this technique”)  
- Scope purple-team exercises  

It is not a compliance checklist. Program use: [GRC/1](../GRC/1_NIST_ISO_And_ATTCK.md). Full matrix: [attack.mitre.org](https://attack.mitre.org/).

For adversary behavior **involving AI** (attacks on AI systems, abuse of AI capabilities, harmful autonomous behavior), use [MITRE ATLAS](https://atlas.mitre.org/) alongside ATT&CK. Literacy: [Threats/8](../Threats/8_AI_Era_Threats.md).

## Pyramid of Pain (detection literacy)

Not all indicators are equal. Rough ordering from easy for attackers to change → hard:

```text
TTPs          ← hardest for attacker to change (most defensive value)
Tools
Network / host artifacts
Domain names
IP addresses
Hash values   ← easiest for attacker to change
```

| Layer | Defensive note |
|-------|----------------|
| Hashes / IPs / domains | Fast blocks and hunting; attackers rotate them cheaply |
| Tools / TTPs | Force real retooling; map to ATT&CK behaviors |

Use cheap indicators for speed; invest engineering in behavioral detections.

## Vocabulary bridge (red and blue)

| Term | Meaning |
|------|---------|
| Vulnerability | Weakness that can be abused |
| Exploit | Method that uses a vulnerability |
| Payload | What runs after successful exploitation |
| IOC | Observable artifact suggesting compromise |
| TTP | Behavior pattern (tactics, techniques, procedures) |
| Event vs incident | Suspicious activity vs confirmed security incident needing response |

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] Recite Kill Chain stages in order  
- [ ] Name the four Diamond vertices  
- [ ] Explain why TTPs sit above hashes on the Pyramid of Pain  
- [ ] Give one use of ATT&CK that is not “compliance”  

## Next

- [Threat intelligence literacy](./19_Threat_Intelligence_Literacy.md)

## Further reading

- [Lockheed Martin — Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)
- [The Diamond Model of Intrusion Analysis (Caltagirone, Pendergast, Betz)](https://www.activeresponse.org/wp-content/uploads/2013/07/diamond.pdf)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [Pyramid of Pain (David Bianco)](https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html)
