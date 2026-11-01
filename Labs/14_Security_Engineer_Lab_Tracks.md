# Security engineer lab tracks

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

This series trains a **security engineer** (cybersecurity engineer, AppSec, SOC/detection, pentester, cloud security, identity-focused security — pick a lane). Theory folders teach the program; **this note tells you what to do in the lab** and which **popular labs** people actually use. Authorized targets only ([1](./1_Authorized_Practice_And_Ethics.md)).

```text
Read a Security-Deep-Dive folder ──▶ Do the matching lab below ──▶ Notes + purple ([2](./2_Practice_Loop_And_Notes.md), [11](./11_Purple_Detect_What_You_Did.md))
```

## Popular labs (know these names)

| Lab / platform | What it is | Typical engineer use |
|----------------|------------|----------------------|
| **[TryHackMe](https://tryhackme.com/)** | Guided rooms + learning paths (AttackBox) | First hands-on for almost every beginner security engineer |
| **[Hack The Box](https://www.hackthebox.com/)** / **[HTB Academy](https://academy.hackthebox.com/)** | Unguided machines + guided modules; Pro Labs for networks | Pentest / red-team realism after THM |
| **[PortSwigger Web Security Academy](https://portswigger.net/web-security)** | Free web vuln topics + interactive labs | **Must-do** for AppSec / web pentesters |
| **[OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)** | Modern vulnerable shop (local Docker) | AppSec drills + scoreboard practice |
| **[OWASP WebGoat](https://owasp.org/www-project-webgoat/)** | Lesson-style vulnerable Java app | Structured web teaching locally |
| **[DVWA](https://github.com/digininja/DVWA)** | Classic PHP vuln app with difficulty levels | Same bug class at low→high defenses |
| **[OverTheWire](https://overthewire.org/wargames/)** (Bandit, Natas, …) | SSH/web wargames | Linux + web fundamentals |
| **[picoCTF](https://picoctf.org/)** | Beginner-friendly CTF problems | Breadth before specialization |
| **[CyberDefenders](https://cyberdefenders.org/)** | Blue-team / DFIR challenges | SOC / DFIR engineers |
| **[LetsDefend](https://letsdefend.io/)** | Simulated SOC investigations | Alert triage practice |
| **[VulnHub](https://www.vulnhub.com/)** | Downloadable vulnerable VMs | Offline unguided boxes (isolate!) |
| **[GOAD](https://github.com/Orange-Cyberdefense/GOAD)** | Vulnerable AD lab | Windows / AD security engineers |
| **[DetectionLab](https://github.com/clong/DetectionLab)** | AD + logging stack for defenders | Detection engineering practice |
| **[CloudGoat](https://github.com/RhinoSecurityLabs/cloudgoat)** | Vulnerable-by-design cloud scenarios | Cloud security engineers (throwaway account) |
| **[Metasploitable 3](https://github.com/rapid7/metasploitable3)** | Vulnerable Windows/Linux VMs | Local exploit practice (isolated) |
| **[Vulhub](https://github.com/vulhub/vulhub)** | CVE labs as Docker Compose | Patch/exploit class drills (lab only) |

You do **not** need every row. You need the rows that match your engineer track below.

## Basic apply

### Pick your engineer track (one primary)

| If you want to become… | Lab you do this month | Deep-Dive folders to read in parallel |
|------------------------|----------------------|----------------------------------------|
| **Security engineer (generalist)** | THM fundamentals path + PortSwigger Apprentice (2 topics) + Bandit | Foundations → Threats → Labs 1–4, 6, 10 |
| **AppSec / product security engineer** | PortSwigger (daily) + Juice Shop local + DVWA ladder | AppSec · Cryptography · Identity (OAuth) |
| **SOC / detection / DFIR engineer** | LetsDefend or CyberDefenders + THM SOC + one Wazuh lab | Defensive-Ops · Threat-Intelligence |
| **Penetration tester / red team** | THM Jr-style path → HTB Academy → easy HTB boxes | Offensive · Reconnaissance · AppSec |
| **Cloud security engineer** | CloudGoat (own account) + cloud rooms on THM/HTB if available | Cloud-Security · Defensive-Ops/10 · Identity |
| **Identity / IAM security engineer** | Local Keycloak lab + OAuth Academy topics + IdP logging purple | Identity · Tooling Identity · Defensive-Ops/10 |
| **GRC / risk-leaning security** | Light: one THM intro + tabletop-style notes; less exploit volume | GRC · Foundations · Threats |

### Weekly ritual for any track

1. **Read** one Security-Deep-Dive note (concept side).  
2. **Do** one lab block from the matching popular platform (60–120 minutes).  
3. **Write** the note template ([2](./2_Practice_Loop_And_Notes.md)).  
4. **Add purple** if you attacked anything ([11](./11_Purple_Detect_What_You_Did.md)).  
5. **Install** missing tools only from [13](./13_Labs_Tooling_Map.md) / Tooling — not random blogs.

```text
Mon–Tue: read series note
Wed–Thu: popular lab (THM / PortSwigger / Juice Shop / …)
Fri:     purple + path-map check ([12](./12_Path_Map.md))
```

### Minimum “I am practicing like an engineer” bar

| Bar | Meaning |
|-----|---------|
| Authorized | Platform scope or your isolated lab only |
| Named lab | You can say which popular lab you used this week |
| Transfer | You can teach the idea without the walkthrough open |
| Linked | Note points at a Deep-Dive folder (AppSec, Defensive-Ops, …) |

## Advanced apply

### 90-day engineer builds (pick one)

#### AppSec engineer

| Phase | Do in the lab |
|-------|----------------|
| Days 1–30 | PortSwigger Apprentice all core topics; Juice Shop easy set |
| Days 31–60 | Practitioner labs; DVWA high; WrongSecrets intro |
| Days 61–90 | Blind Juice Shop hard; map each find to AppSec + fix notes |

#### SOC / detection engineer

| Phase | Do in the lab |
|-------|----------------|
| Days 1–30 | LetsDefend or CyberDefenders beginner; Wireshark fluency |
| Days 31–60 | THM SOC path segment; stand up Wazuh lab; replay one attack and find it |
| Days 61–90 | DetectionLab or equivalent; write 3 detections; purple every offensive side-quest |

#### Pentester

| Phase | Do in the lab |
|-------|----------------|
| Days 1–30 | THM structured path + OverTheWire Bandit; Nmap daily |
| Days 31–60 | HTB Academy modules; 8–12 easy boxes; PortSwigger SQLi/XSS |
| Days 61–90 | Medium boxes; GOAD light **or** HTB AD intro; Pro Lab only if ready |

#### Cloud security engineer

| Phase | Do in the lab |
|-------|----------------|
| Days 1–30 | Cloud-Security notes + one CloudGoat scenario; destroy after |
| Days 31–60 | Three more scenarios; rebuild timeline from cloud logs |
| Days 61–90 | Purple IR runbooks; optional platform cloud rooms |

### Do not confuse popularity with permission

Popular does **not** mean “attack anything famous on the internet.” Popular means **widely used authorized training surfaces**. Stay inside their rules and your isolated deployments ([3](./3_Local_Lab_Hygiene.md)).

### Capstone (any track)

Ship a **personal portfolio packet** (private is fine):

1. Five lab writeups in your own words (dated).  
2. One purple detection you proved in a lab SIEM or log stack.  
3. One hardened fix (code/config) for a vuln you exploited in Juice Shop / DVWA / WebGoat.  
4. A one-page “track” statement: role you want + labs you used + Deep-Dive folders completed.

That packet is closer to hiring signal than a streak counter alone.

## Next

- Return to [Path map](./12_Path_Map.md) for the skill matrix · [Tooling map](./13_Labs_Tooling_Map.md) for installs

## Further reading

- [TryHackMe](https://tryhackme.com/) · [Hack The Box](https://www.hackthebox.com/) · [HTB Academy](https://academy.hackthebox.com/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) · [WebGoat](https://owasp.org/www-project-webgoat/)
- [OverTheWire](https://overthewire.org/wargames/) · [picoCTF](https://picoctf.org/)
- [CyberDefenders](https://cyberdefenders.org/) · [LetsDefend](https://letsdefend.io/)
- [CloudGoat](https://github.com/RhinoSecurityLabs/cloudgoat) · [GOAD](https://github.com/Orange-Cyberdefense/GOAD)
- [NICE Framework](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center)
- [CISA workforce training guide](https://www.cisa.gov/sites/default/files/publications/Cybersecurity%20Workforce%20Training%20Guide_508c.pdf)
