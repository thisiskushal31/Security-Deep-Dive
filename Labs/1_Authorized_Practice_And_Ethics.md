# Authorized practice and ethics

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

A cyber range (or lab platform) exists so you can build skill in a **safe, legal** environment — simulated networks, apps, and tools without touching someone else’s production. The hard rule never changes: **no authorization → no testing.**

## Basic apply

### Decide if the target is in-bounds

Before any scan, exploit, or “just checking,” answer yes to **one** of these:

| Allowed | Not allowed |
|---------|-------------|
| The platform’s assigned machine / room / lab | Random websites, school Wi‑Fi, employer prod “for learning” |
| A vulnerable app **you** deployed on an isolated network | Someone else’s cloud account without written approval |
| A competition / range that explicitly authorizes play | “Bug bounty” style probing without a published program |

If you are guessing, stop. Claiming “security research” after unauthorized access does not legalize it.

### Use the three-box checklist

```text
1. Who owns this system?
2. Do I have written / platform-granted permission for THIS action?
3. If this breaks, who gets hurt — and is that acceptable in a lab?
```

Print the checklist into your note template ([2](./2_Practice_Loop_And_Notes.md)). Fail any box → do not proceed.

### Start only on authorized surfaces

| Surface | First move |
|---------|------------|
| TryHackMe / HTB / PortSwigger lab | Use their VPN / browser lab / in-scope IP only |
| Local Juice Shop / WebGoat / DVWA | Bind to localhost; host-only / internal net — [3](./3_Local_Lab_Hygiene.md) |
| CloudGoat scenario | Dedicated throwaway cloud account you own |

## Advanced apply

### Separate “can” from “may”

Skill growth tempts scope creep (pivot to a neighbor, scrape the platform infra, test a friend’s SaaS). Treat **scope** like a contract:

| Practice | Harden your habit |
|----------|-------------------|
| Document scope in the first line of notes | “In scope: THM room IP only” |
| Time-box offensive sessions | When the timer ends, stop pivoting fantasies |
| Prefer platform write-permission over clever bypass of platform rules | Ban evasion is still out of scope |

### Align practice to roles (without chasing badges)

Workforce frameworks (NICE) describe work roles — useful when picking **what** to practice next, not as a license to practice on live estates. Map a week of labs to a role intent (e.g. protect-and-defend vs securely provision), then pick platforms in [12](./12_Path_Map.md).

### Build an “authorization folder” for personal labs

For anything self-hosted or cloud:

1. One paragraph: purpose, owner (you), isolation method.  
2. Screenshot or note of account ID / VPC / hypervisor network mode.  
3. Destroy / tear-down date.  
4. Explicit “not connected to production IdP / prod data.”

That packet is what you show yourself when tempted to “quickly” peer the lab into a real network.

## Next

- [Local lab hygiene](./3_Local_Lab_Hygiene.md) (then return to [practice loop](./2_Practice_Loop_And_Notes.md))

## Further reading

- [NIST — Cyber Ranges](https://www.nist.gov/system/files/documents/2018/02/13/cyber_ranges.pdf)
- [NIST — The Cyber Range: A Guide](https://www.nist.gov/document/cyber-range)
- [NIST — Guide to Cybersecurity Competitions (competitors)](https://www.nist.gov/system/files/documents/2023/09/29/Guide%20for%20Competitors.pdf)
- [TryHackMe — Where to practice hacking legally](https://tryhackme.com/resources/blog/vulnerable-machines-and-sites-where-to-practice-hacking-legally)
- [CISA — Cybersecurity training & exercises](https://www.cisa.gov/topics/cybersecurity-best-practices/cybersecurity-training-exercises)
