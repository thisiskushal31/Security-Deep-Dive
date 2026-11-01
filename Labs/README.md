# Labs

[← README](../README.md)

This folder is the **hands-on engine** of Security-Deep-Dive: it turns program knowledge into a **security engineer** practice habit (AppSec, SOC/detection, pentest, cloud, identity — choose a track). Methodology stays in Offensive / Defensive-Ops / AppSec / Recon; installs stay in [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security).

Labs notes are **apply-first**: **basic apply → advanced apply**.

```text
Become a security engineer
        │
        ▼
Read Deep-Dive folder ──▶ Practice on a popular authorized lab ──▶ Notes + purple
        │
        ▼
Track map ([14](./14_Security_Engineer_Lab_Tracks.md)) · Path matrix ([12](./12_Path_Map.md))
```

**Never scan or attack systems you do not own or lack explicit written authorization to test.**

### Popular labs (start here)

| Popular lab | Use it when… |
|-------------|--------------|
| [TryHackMe](https://tryhackme.com/) | Guided paths; first offensive + blue rooms |
| [HTB Academy](https://academy.hackthebox.com/) / [Hack The Box](https://www.hackthebox.com/) | Modules then unguided boxes / Pro Labs |
| [PortSwigger Web Security Academy](https://portswigger.net/web-security) | Web AppSec (free interactive labs) |
| [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) · [WebGoat](https://owasp.org/www-project-webgoat/) · [DVWA](https://github.com/digininja/DVWA) | Local web vuln apps (isolate — [3](./3_Local_Lab_Hygiene.md)) |
| [OverTheWire](https://overthewire.org/wargames/) · [picoCTF](https://picoctf.org/) | Linux / CTF fundamentals |
| [CyberDefenders](https://cyberdefenders.org/) · [LetsDefend](https://letsdefend.io/) | SOC / DFIR |
| [GOAD](https://github.com/Orange-Cyberdefense/GOAD) · [DetectionLab](https://github.com/clong/DetectionLab) | AD attack / defend estates |
| [CloudGoat](https://github.com/RhinoSecurityLabs/cloudgoat) | Cloud vulnerable-by-design (your account only) |

Full track → lab mapping: **[14 — Security engineer lab tracks](./14_Security_Engineer_Lab_Tracks.md)**.

## Learning path

| # | Topic |
|---|--------|
| 1 | [Authorized practice and ethics](./1_Authorized_Practice_And_Ethics.md) |
| 2 | [Practice loop and notes](./2_Practice_Loop_And_Notes.md) |
| 3 | [Local lab hygiene](./3_Local_Lab_Hygiene.md) |
| 4 | [Guided platforms (THM / HTB Academy)](./4_Guided_Platforms_THM_HTB_Academy.md) |
| 5 | [Unguided boxes and Pro Labs](./5_Unguided_Boxes_And_Pro_Labs.md) |
| 6 | [Web AppSec labs](./6_Web_AppSec_Labs.md) |
| 7 | [Blue, SOC, and DFIR labs](./7_Blue_SOC_And_DFIR_Labs.md) |
| 8 | [AD and Windows estates](./8_AD_And_Windows_Estates.md) |
| 9 | [Cloud vulnerable-by-design](./9_Cloud_Vulnerable_By_Design.md) |
| 10 | [Wargames and CTF fundamentals](./10_Wargames_And_CTF_Fundamentals.md) |
| 11 | [Purple: detect what you did](./11_Purple_Detect_What_You_Did.md) |
| 12 | [Path map](./12_Path_Map.md) |
| 13 | [Labs tooling map](./13_Labs_Tooling_Map.md) |
| 14 | [Security engineer lab tracks](./14_Security_Engineer_Lab_Tracks.md) |

**Suggested order:** 1 → 3 → 2 → **14** (pick your engineer track) → 4 → 6 → 10 → 5 → 7 → 8 → 9 → 11 → 12 → 13.

## What maps where

| Concern | Note |
|---------|------|
| Legal / authorized-only | 1, 3 |
| Engineer career → which lab | **14**, 12 |
| How to practice and record | 2, 11 |
| Guided vs unguided platforms | 4, 5, 10 |
| Web / blue / AD / cloud ranges | 6–9 |
| Tool installs | 13 → Tooling |

## Depth elsewhere (do not duplicate)

| Topic | Home |
|-------|------|
| PTES-shaped attack phases | [Offensive](../Offensive/README.md) |
| Recon methodology | [Reconnaissance](../Reconnaissance/README.md) |
| SOC / IR / hunt | [Defensive-Ops](../Defensive-Ops/README.md) |
| Web defect classes | [AppSec](../AppSec/README.md) |
| Identity lab IdPs | [Identity](../Identity/README.md) |
| Scanner / Burp / Wireshark installs | [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security) |

## Further reading (suite anchors)

- [NIST — Cyber Ranges](https://www.nist.gov/system/files/documents/2018/02/13/cyber_ranges.pdf)
- [NIST — Guide to Cybersecurity Competitions (competitors)](https://www.nist.gov/system/files/documents/2023/09/29/Guide%20for%20Competitors.pdf)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [TryHackMe](https://tryhackme.com/) · [Hack The Box](https://www.hackthebox.com/) · [HTB Academy](https://academy.hackthebox.com/)
- [NICE Framework](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center)
