# Threats, vulnerabilities, mitigations

[← README](../README.md)

This folder is the **threat deep dive**: what can go wrong, who or what causes it, how it arrives, what weaknesses it abuses, what malware and attack actions look like, how you notice it, how you reduce it — and how the **AI era** changes the picture without erasing classical cyber risk.

Read in order. Each note is self-contained enough that you leave with a clear mental model; **Further reading** at the bottom of every note is where you go for full advisories, taxonomies, and campaign write-ups.

Ordered **concept → advanced → apply**. No exploit recipes; no invented actor lore; no jailbreak payloads. Each note’s **Real cases** section cites official public sources (CISA, NIST, FBI IC3, MITRE, ENISA, CCCS, OWASP) to show *these threats have already happened* — understanding only, not how to reproduce them.

```text
1 Actors → 2 Vectors/surfaces → 3 Vulnerabilities
  → 4 Malware → 5 Attack classes → 6 Indicators → 7 Mitigations
  → 8 AI-era threats
```

## How to use this folder

| If you want… | Start here |
|--------------|------------|
| The full map of threat *kinds* | Sections below, then notes `1 → 8` |
| “Who attacks and why?” | [1](./1_Actors_And_Motivations.md) |
| “How do they get in?” | [2](./2_Vectors_And_Surfaces.md) |
| “What weakness did they use?” | [3](./3_Vulnerabilities.md) |
| “What malicious software?” | [4](./4_Malware.md) |
| “What action / technique class?” | [5](./5_Attack_Classes.md) |
| “What should I look for?” | [6](./6_Indicators_And_Analysis.md) |
| “What do we do about it?” | [7](./7_Mitigations.md) |
| “What’s new with AI?” | [8](./8_AI_Era_Threats.md) |

**One chain to remember:**

```text
Threat source → vector → vulnerability → (malware / attack action)
        → indicators → incident? → mitigations / recovery
```

AI-era risk sits *on top of* that chain: same actors and many same vectors, plus attacks **on** AI systems and failures **of** AI design.

## Topic files

| # | Topic | Status |
|---|--------|--------|
| 1 | [Actors and motivations](./1_Actors_And_Motivations.md) | filled |
| 2 | [Vectors and attack surfaces](./2_Vectors_And_Surfaces.md) | filled |
| 3 | [Vulnerabilities](./3_Vulnerabilities.md) | filled |
| 4 | [Malware](./4_Malware.md) | filled |
| 5 | [Attack classes](./5_Attack_Classes.md) | filled |
| 6 | [Indicators and analysis](./6_Indicators_And_Analysis.md) | filled |
| 7 | [Mitigations](./7_Mitigations.md) | filled |
| 8 | [AI-era threats](./8_AI_Era_Threats.md) | filled |

## Learning path

`1 → 2 → 3 → 4 → 5 → 6 → 7 → 8` → [AppSec](../AppSec/README.md) or [Defensive-Ops](../Defensive-Ops/README.md) · AI build depth → [AI-Security](../AI-Security/README.md) (**stub**) · lab → [Offensive](../Offensive/README.md)

## What kinds of threats are covered (landscape)

Use this as the “I open Threats and see the whole board” view. Detail lives in the numbered notes.

### Classical cyber threats (still the majority of real incidents)

| Kind | What you should understand | Home |
|------|----------------------------|------|
| **Adversarial actors** | Nation-state, crime, hacktivist, insider, unskilled — capability, intent, targeting | [1](./1_Actors_And_Motivations.md) |
| **Non-adversarial sources** | Accident, structural failure, environmental disaster — same impact language, different response | [1](./1_Actors_And_Motivations.md) |
| **Social engineering & influence** | Phishing, BEC, vishing/smishing, pretexting, deepfake-assisted fraud, mis/disinformation | [2](./2_Vectors_And_Surfaces.md) · [8](./8_AI_Era_Threats.md) |
| **Network & remote entry** | Exposed services, VPN/RDP abuse, wireless evil twin, on-path, DDoS | [2](./2_Vectors_And_Surfaces.md) · [5](./5_Attack_Classes.md) |
| **Supply chain & trust** | Compromised updates/vendors/MSPs; dependency/trojan packages | [2](./2_Vectors_And_Surfaces.md) · [3](./3_Vulnerabilities.md) |
| **Software & config weaknesses** | Injection, XSS, overflow, TOCTOU, misconfig, EOL, zero-day, KEV-prioritized vulns | [3](./3_Vulnerabilities.md) |
| **Malware families** | Virus, worm, Trojan, ransomware, spyware, rootkit, bot/botnet, logic bomb | [4](./4_Malware.md) |
| **Application / password / crypto attacks** | Injection, traversal, privilege escalation, spray/stuff/brute, downgrade, collision | [5](./5_Attack_Classes.md) |
| **Physical** | Forced entry, badge cloning, environmental attacks on facilities | [5](./5_Attack_Classes.md) |

### AI-era threats (amplify + new surface)

| Kind | What you should understand | Home |
|------|----------------------------|------|
| **Attacks using AI** | Better phishing, voice clone, deepfake, malware assist, recon assist, AI-as-lure | [8](./8_AI_Era_Threats.md) |
| **Attacks on AI** | Poisoning, evasion, prompt injection, model extraction, agent goal hijack, MCP/tool abuse | [8](./8_AI_Era_Threats.md) |
| **AI design / ops failures** | Brittleness, overreliance, shadow AI, weak agency controls | [8](./8_AI_Era_Threats.md) |

### Real cases that already happened

These are **not** a hall-of-fame of attackers. They prove the classes above are not theoretical. Full context is in each note’s **Real cases** section and Further reading.

| Pattern that keeps recurring | Official / primary anchor | Note |
|------------------------------|---------------------------|------|
| **Phishing / spoofing at massive volume** | FBI IC3: phishing/spoofing leads complaint counts year after year | [2](./2_Vectors_And_Surfaces.md) |
| **Business email compromise (BEC)** | FBI IC3: multi-billion USD annual reported losses; long-running scam class | [2](./2_Vectors_And_Surfaces.md) |
| **Wormable ransomware / destructive wiper-as-ransom** | CISA: WannaCry; NotPetya (2017) — unpatched SMB-class flaws + fast lateral spread | [4](./4_Malware.md) |
| **Ransomware hitting critical infrastructure IT** | CISA/FBI: DarkSide against a U.S. pipeline company’s IT (2021) — OT risk via business disruption | [4](./4_Malware.md) · [7](./7_Mitigations.md) |
| **Software supply-chain compromise** | CISA: SolarWinds Orion supply-chain campaign (2020) — trusted update as delivery | [2](./2_Vectors_And_Surfaces.md) · [3](./3_Vulnerabilities.md) |
| **Internet-facing library RCE, mass exploitation** | CISA: Log4Shell (CVE-2021-44228) — ubiquitous logging library, active exploitation | [3](./3_Vulnerabilities.md) |
| **Known-exploited vulns as priority signal** | CISA KEV catalog — evidence-based patch urgency | [3](./3_Vulnerabilities.md) |
| **Incident shapes defenders actually see** | NIST SP 800-61r3 examples: botnet DoS, ransomware + theft, phishing→fraud, appliance 0-day, compromised vendor software | [5](./5_Attack_Classes.md) · [6](./6_Indicators_And_Analysis.md) |
| **GenAI-amplified fraud / deepfake impersonation** | CCCS AI security actions cite deepfake video-call fraud patterns | [8](./8_AI_Era_Threats.md) |
| **Prompt injection against enterprise assistants** | Documented class in NIST AML / OWASP LLM; CCCS cites production assistant injection cases | [8](./8_AI_Era_Threats.md) |
| **AI used as weapon, target, and lure** | ENISA Threat Landscape 2025 | [8](./8_AI_Era_Threats.md) |

## Topic map (Domain 2 + AI-era)

| Domain 2 / AI area | Home |
|--------------------|------|
| Actors, attributes, motivations; NIST source types; APT/sophistication; attribution | 1 |
| Every listed vector/surface; social engineering glossary; MSP/supply chain | 2 |
| Every listed vulnerability type; CVE/CVSS/CWE/zero-day; KEV | 3 |
| Malware list + ransomware forms + botnet/backdoor | 4 |
| Physical, network, application, crypto, password attacks | 5 |
| Indicator list + analysis habits | 6 |
| Mitigation + hardening lists + path playbooks | 7 |
| Attacks using AI; attacks on AI (AML/LLM/ASI); NIST AI 600-1 GAI risks; AI design failures; AIBOM/supply chain; ATLAS | 8 |

Network packet labs: [Networks-Deep-Dive Security](https://github.com/thisiskushal31/Networks-Deep-Dive). ATT&CK catalog: [GRC](../GRC/1_NIST_ISO_And_ATTCK.md). AI TTPs: [MITRE ATLAS](https://atlas.mitre.org/).

## Next after Threats

| Goal | Folder |
|------|--------|
| Build secure software | [AppSec](../AppSec/README.md) |
| Secure LLM / agent products | [AI-Security](../AI-Security/README.md) (**stub**) |
| Detect and respond | [Defensive-Ops](../Defensive-Ops/README.md) |
| Authorized lab offense | [Offensive](../Offensive/README.md) |
| Framework nesting | [GRC](../GRC/README.md) |
