# OSINT

[← Offensive](./README.md)

**Open-source intelligence**: public facts about the target *within scope* (domains, employees’ public profiles, leaked pastes you are allowed to use, certificate transparency). Lab OSINT often uses intentionally public lab brands.

## What you collect

| Class | Example use |
|-------|-------------|
| Domains / subdomains | Attack surface map |
| Public IPs / ASN | What is internet-facing |
| Tech fingerprints | Focus later scans |
| People / email patterns | Phishing *only if RoE allows* |

## Tools (install elsewhere)

theHarvester, Amass, Maltego, SpiderFoot, Shodan → [Tooling Reconnaissance](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance). This note is the **phase**, not the CLI.

## Boundaries

- Do not social-engineer real people without explicit RoE.  
- Do not scrape in ways that violate law or ToS for “practice.”  
- Prefer lab-provided OSINT targets when learning.

## Next

- [Scanning and enumeration](./4_Scanning_And_Enumeration.md)
