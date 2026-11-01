# Guided platforms (THM / HTB Academy)

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Guided platforms teach **concept → short exercise → questions** inside an authorized cloud lab. For a beginning **security engineer**, these are usually the first popular labs after ethics/hygiene — before unguided boxes ([5](./5_Unguided_Boxes_And_Pro_Labs.md)). Role tracks: [14](./14_Security_Engineer_Lab_Tracks.md).

## Basic apply

### Pick a lane and finish a path segment

| Popular platform | Best first use for an engineer |
|------------------|--------------------------------|
| **TryHackMe** | Learning paths / modules with AttackBox; default first lab home for most beginners (offense + some blue) |
| **HTB Academy** | Module-based skill tracks with Pwnbox; job-role style modules before live HTB machines |

```text
Account ──▶ Join a path / module ──▶ Spawn lab ──▶ Follow tasks ──▶ Notes ([2](./2_Practice_Loop_And_Notes.md))
```

### First week plan (example)

1. Complete platform orientation (VPN or browser lab connectivity).  
2. Finish one **Linux fundamentals** style module/room set.  
3. Finish one **network scanning** intro (pair with [Tooling Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap)).  
4. Finish one **web** intro room — then deepen on PortSwigger / Juice Shop ([6](./6_Web_AppSec_Labs.md)).  
5. Keep notes dated; no writeup pasting.

### Stay inside the platform fence

| Do | Do not |
|----|--------|
| Attack only the assigned machine/IP | Scan platform infrastructure or other users |
| Use provided AttackBox/Pwnbox when stuck on setup | Expose your home lab while “also” on THM VPN carelessly |
| Read room rules / writeups policy | Share answers in public channels against ToS |

## Advanced apply

### Graduate on purpose

| You are ready to leave pure guidance when… | Next step |
|--------------------------------------------|-----------|
| You finish tasks without opening hints first | Retry the same topic on an easy unguided box ([5](./5_Unguided_Boxes_And_Pro_Labs.md)) |
| You can name the MITRE technique / OWASP class | Tag the note; add a detection line ([11](./11_Purple_Detect_What_You_Did.md)) |
| Setup is no longer the bottleneck | Prefer local labs for web ([3](./3_Local_Lab_Hygiene.md), [6](./6_Web_AppSec_Labs.md)) |

### Build a dual track

| Day type | Guided platform | Parallel |
|----------|-----------------|----------|
| Learn | THM / Academy module | Skim matching Security-Deep-Dive note |
| Prove | Same skill, harder room | Blind attempt, hints last |
| Defend | THM SOC / blue intro | [7](./7_Blue_SOC_And_DFIR_Labs.md) |

### Avoid path theater

Completing fifty rooms with empty notes is weaker than ten rooms with transfer notes. Cap vanity metrics; measure **retry-blind success** and **detection ideas logged**.

## Next

- [Web AppSec labs](./6_Web_AppSec_Labs.md)

## Further reading

- [TryHackMe](https://tryhackme.com/)
- [TryHackMe — legal practice overview](https://tryhackme.com/resources/blog/vulnerable-machines-and-sites-where-to-practice-hacking-legally)
- [HTB Academy](https://academy.hackthebox.com/)
- [Hack The Box](https://www.hackthebox.com/)
