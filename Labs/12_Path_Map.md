# Path map

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

A path map turns random rooms into a **security-engineer curriculum**. Pick a role first in [14](./14_Security_Engineer_Lab_Tracks.md), then use this matrix for week-to-week lab choices. Date progress in personal notes — not vanity checkboxes in this public repo.

## Basic apply

### Skills → popular labs → tools

| Skill focus | Popular lab to use | Then | Install first (Tooling) |
|-------------|-------------------|------|-------------------------|
| Linux + networking | **OverTheWire Bandit** · **TryHackMe** intro | Easy **Hack The Box** / VulnHub box | Nmap, Wireshark |
| Web AppSec | **PortSwigger Academy** · **Juice Shop** local | **DVWA** ladder · THM web · **WebGoat** | Burp or ZAP, ffuf |
| Guided offense | **TryHackMe** path · **HTB Academy** | Easy **HTB** machines | Nmap, Impacket (later) |
| Blue / SOC | **LetsDefend** · **CyberDefenders** · THM SOC | **DetectionLab** + Wazuh | Wireshark; Volatility later |
| Fundamentals CTF | **picoCTF** easy mix | Harder categories / Bandit deep | Per-category tools |
| AD / Windows | After single-host comfort | **GOAD** light · HTB AD/**Pro Labs** | BloodHound, Impacket |
| Cloud | **CloudGoat** (throwaway account) | More scenarios + IR notes | Cloud CLIs (lab profiles only) |
| Purple | Any success above + purple section | DetectionLab / SIEM retest | Wazuh / osquery |

Always stay inside platform rules and isolated local/cloud labs ([1](./1_Authorized_Practice_And_Ethics.md), [3](./3_Local_Lab_Hygiene.md)).

### 30-day starter (example security engineer)

```text
Week 1: Ethics + hygiene + OverTheWire Bandit + note template
Week 2: PortSwigger Apprentice (2 topics) + Juice Shop easy
Week 3: TryHackMe or HTB Academy path segment + one easy HTB/VulnHub box
Week 4: LetsDefend or CyberDefenders challenge + purple on all offensive notes
```

## Advanced apply

### Role-shaped tracks (detail in [14](./14_Security_Engineer_Lab_Tracks.md))

| Aim | Popular labs to live in | De-emphasize early |
|-----|-------------------------|--------------------|
| AppSec engineer | PortSwigger · Juice Shop · DVWA · WebGoat | Full AD forests |
| SOC / detection engineer | LetsDefend · CyberDefenders · DetectionLab · Wazuh lab | Binary exploitation deep |
| Pentester | THM → HTB Academy → HTB boxes → GOAD / Pro Labs | Skipping web fundamentals |
| Cloud security engineer | CloudGoat (+ platform cloud rooms) | Random CTF pwn only |

### Progression gates (promote yourself)

| From | Gate to next |
|------|----------------|
| Guided only | Blind easy box without writeup open |
| Easy boxes | Medium box or small AD light lab |
| Single host | Multi-host / Pro Lab |
| Offense-only notes | ≥50% notes with purple section |

### Cross-links into the series

| After practicing… | Read / revisit |
|-------------------|----------------|
| Recon-heavy box | [Reconnaissance](../Reconnaissance/README.md) |
| Web bugs | [AppSec](../AppSec/README.md) |
| Cred / token abuse | [Identity](../Identity/README.md) |
| IR / hunt drills | [Defensive-Ops](../Defensive-Ops/README.md) |
| PTES-shaped engagements | [Offensive](../Offensive/README.md) |

## Next

- [Security engineer lab tracks](./14_Security_Engineer_Lab_Tracks.md) · [Labs tooling map](./13_Labs_Tooling_Map.md)

## Further reading

- [TryHackMe](https://tryhackme.com/) · [HTB Academy](https://academy.hackthebox.com/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [OverTheWire](https://overthewire.org/wargames/) · [picoCTF](https://picoctf.org/)
- [NICE Framework resource center](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center)
- [CISA workforce training guide](https://www.cisa.gov/sites/default/files/publications/Cybersecurity%20Workforce%20Training%20Guide_508c.pdf)
