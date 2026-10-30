# Vectors and attack surfaces

[← Threats](./README.md)

> **Progression:** concept → advanced → apply.

How attacks arrive, and everything that can receive them — the **paths** between a threat source and your assets. If note [1](./1_Actors_And_Motivations.md) is *who/why*, this note is *how it shows up*. Human-vector literacy also: [Foundations/16](../Foundations/16_Human_Security_Literacy.md). Packet depth: [Networks-Deep-Dive Security](https://github.com/thisiskushal31/Networks-Deep-Dive).

## Concept

| Term | Meaning |
|------|---------|
| Threat / attack vector | Path or method used to reach the target |
| Attack surface / cyber threat surface | All systems and services an actor may try to exploit — internet-exposed endpoints, processes that rely on connected systems, and (for individuals) how widely personal data is shared online |
| Entry point | Concrete reachable item (port, mailbox, USB, API, VPN, partner link) |

```text
Threat source → vector → attack surface / entry → vulnerability → impact
```

The larger the surface, the harder it is to secure. Endpoints include PCs, mobiles, IoT/IIoT, servers, and OT. Supply chains and managed-service trust relationships expand surface indirectly: compromise a supplier or MSP with elevated access, then reach the intended victim.

Actors get in by **vulnerability exploitation**, **social engineering**, and/or **influence content** (false or misleading information). Social engineering is extremely common and often combined with technical exploits (for example a phishing link that triggers an exploit).

**Initial Access** techniques (foothold vocabulary) include: phishing, exploit public-facing application, external remote services, valid accounts, supply-chain compromise, trusted relationship, removable media, drive-by compromise, hardware additions, content injection, Wi-Fi networks.

## Advanced

### Message-, file-, image-, and voice-based vectors

| Vector | Definition | First controls |
|--------|------------|----------------|
| **Email** | Messages used to deliver lures, malware, or credential theft | Gateway filters; external-mail flags; attachment type controls; DMARC/SPF/DKIM; disable internet macros; report button; phishing-resistant MFA |
| **SMS (smishing)** | Social engineering or malware links over text | Out-of-band verify; phishing-resistant MFA; user training |
| **Instant messaging** | Chat/apps used like email for lures and credential theft | App allow lists; verify high-risk requests |
| **Image-based** | Malicious or lure content via images (including stego-class hiding) | Patch viewers; treat untrusted media carefully |
| **File-based** | Hostile payloads in documents, archives, installers | Attachment filters; sandbox; EDR; block risky macros |
| **Voice call (vishing)** | Social engineering by phone — including **AI voice cloning** of trusted people | Identity-proofing; callback to known numbers; never trust voice alone for money/access ([8](./8_AI_Era_Threats.md)) |
| **Synthetic media / deepfake** | AI-generated audio, image, or video used as a lure or influence vector | Out-of-band verify; dual control; treat unexpected media as untrusted ([8](./8_AI_Era_Threats.md)) |
| **Model / agent / RAG APIs** | Attack surface when you *deploy* AI (prompts, tools, retrieval stores, fine-tune pipelines, MCP) | Least privilege; output validation; inventory models/data ([8](./8_AI_Era_Threats.md)) |
| **Fake AI product / installer** | Malware delivered via sites or packages impersonating popular GenAI tools (AI-as-lure) | Download only from verified vendors; allow lists ([8](./8_AI_Era_Threats.md) · [4](./4_Malware.md)) |
| **Coding-assistant configs / “rules”** | Hostile instructions planted where IDE/AI assistants will load them | Review assistant config; pin/sign tooling; treat assistant context as untrusted ([8](./8_AI_Era_Threats.md)) |

Password-protected archives and novel attachment formats are used to evade naive filters — treat filter lists as living controls. Review high-risk file types at least periodically.

### Software, credentials, ports, and remote access

| Vector | Definition | First controls |
|--------|------------|----------------|
| **Vulnerable software** | Weaknesses in apps/clients/agents/appliances | Patch SLA; remove unused software |
| **Client-based vs agentless** | Local installed software vs browser/remote sessions without a persistent agent | Patch and configure both sides of the session |
| **Unsupported / EOL systems** | Products that no longer receive security fixes | Isolate, compensate, or replace |
| **Open service ports** | Network listeners reachable by attackers | Inventory; close; authenticate remaining |
| **Default credentials** | Factory or “welcome” passwords left unchanged | Change on deploy; scan for defaults; force change on first login |
| **Valid / stolen accounts** | Abuse of real credentials (local, domain, cloud, default) | MFA; detect anomalies; rapid revoke |
| **External remote services** | VPN, VDI, RDP, and similar remote entry | MFA; least exposure; logging; lockouts |
| **Misconfiguration** | Unsafe settings (open storage, debug, overly broad rules) | Baselines; CSPM/IaC policy |

### Network and physical vectors

| Vector | Definition | First controls |
|--------|------------|----------------|
| **Unsecure wired** | Weak or open wired network paths | Segment; 802.1X-class access where appropriate |
| **Unsecure wireless / Wi-Fi** | Weak Wi-Fi (open, weak crypto, evil-twin proximity) | Strong auth; monitor; least privilege on WLAN |
| **Bluetooth** | Wireless personal-area links abused for access or data | Disable when unused; patch; pair carefully |
| **Removable device** | USB and other media as delivery or exfil path | Device control; scanning; disk encryption |
| **Hardware additions** | Rogue devices introduced into the environment | Physical security; port control; inventory |

### Supply chain and trusted relationships

| Vector | Definition | First controls |
|--------|------------|----------------|
| **MSP** | Managed service provider with elevated client access | Least privilege; monitoring; contracts |
| **Vendors / suppliers** | Third parties in software, hardware, or services | Same as MSP; SBOM/signing for software |
| **Software supply chain** | Tamper with dependencies, builds, or update channels | Pin; verify; SCA; signed artifacts |
| **Hardware supply chain** | Tamper with devices/firmware before delivery | Trusted purchasing; firmware discipline |
| **Trusted relationship** | Abuse a partner connection that gets less scrutiny than a direct attack | Treat partner access as high risk |

### Human / social engineering vectors (definitions)

| Technique | Definition |
|-----------|------------|
| **Phishing** | Electronically delivered social engineering (often email); mass or targeted |
| **Spear phishing** | Phishing aimed at a specific person, company, or industry |
| **Vishing** | Social engineering by voice |
| **Smishing** | Social engineering by SMS |
| **Pretexting** | Invented scenario to extract information or action |
| **Impersonation** | Pretending to be a trusted person |
| **Brand impersonation** | Pretending to be a trusted brand/org |
| **Business email compromise (BEC)** | Impersonate executives or trusted parties to trick victims into transferring funds or data — common and costly |
| **Watering hole** | Compromise a website the target group is likely to visit |
| **Typosquatting** | Look-alike domains or package names to deceive users |
| **Misinformation** | False information spread without necessarily intending harm as the primary goal |
| **Disinformation** | False information spread *deliberately* to deceive |
| **Malinformation** | Genuine information used out of context to harm (awareness term in influence discussions) |
| **Drive-by compromise** | User visits a site that delivers exploit code during normal browsing |
| **Exploit kit** | Collection of exploits that probes a visitor’s software and deploys a matching exploit |

```text
Internet / partners / users
        │
   email · SMS · IM · file · voice · USB · web · API · VPN · MSP
        │
   surface inventory → authenticate → authorize → monitor → shrink
```

## Apply

1. Inventory internet-facing and partner-reachable entry points.  
2. Tag each with a vector class from the tables above.  
3. Close unused ports/services; change defaults; retire EOL where possible.  
4. Put MFA on email and privileged remote access; harden mail (filters, macros, DMARC).  
5. Treat MSP/vendor paths as high-value surface.  
6. Monitor what remains ([Defensive-Ops](../Defensive-Ops/README.md)).

### Self-check

- [ ] Define vector vs attack surface vs entry point  
- [ ] Give one-sentence definitions for BEC, watering hole, typosquatting, drive-by  
- [ ] Contrast client-based vs agentless vulnerable software  
- [ ] Explain why MSPs expand surface  
- [ ] Name five Initial Access technique classes  

### Real cases (these already happened)

Short examples from official public reports so you know this is not theory. Links have the full story — this is **not** a how-to.

| What happened | Why it matters here | Where to read more |
|-----------------------|------------------------------|--------------------|
| **Phishing / spoofing** | Still the highest-volume internet crime *complaint type* in FBI IC3 reporting — email/SMS/web remain primary delivery | [FBI IC3 2025 Annual Report](https://www.ic3.gov/AnnualReport/Reports/2025_IC3Report.pdf) |
| **Business email compromise** | Social + process vector: impersonation and mailbox trust drive multi-billion USD annual reported losses | [IC3 BEC PSA](https://www.ic3.gov/PSA/2024/PSA240911) · IC3 annual reports |
| **Trusted update / vendor path** | Supply chain as vector: malicious code shipped inside a normal software lifecycle | [CISA AA20-352a](https://www.cisa.gov/news-events/cybersecurity-advisories/aa20-352a) |
| **AI-amplified social engineering** | Same channels (email, voice, video meeting) with higher persuasiveness | [8](./8_AI_Era_Threats.md) · [CCCS AI security actions](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049) |

AI-amplified social engineering and AI-system entry points: [8](./8_AI_Era_Threats.md).

## Next

- [Vulnerabilities](./3_Vulnerabilities.md)

## Further reading

- [NIST CSRC glossary — attack surface](https://csrc.nist.gov/glossary/term/attack_surface)
- [MITRE ATT&CK — Initial Access (TA0001)](https://attack.mitre.org/tactics/TA0001/)
- [Canadian Centre for Cyber Security — Introduction to the cyber threat environment](https://www.cyber.gc.ca/en/guidance/introduction-cyber-threat-environment)
- [CISA — #StopRansomware Guide](https://www.cisa.gov/stopransomware/ransomware-guide)
- [CISA — Phishing Guidance](https://www.cisa.gov/sites/default/files/2023-10/Phishing%20Guidance%20-%20Stopping%20the%20Attack%20Cycle%20at%20Phase%20One_508c.pdf)
- [FBI IC3 — Annual Reports](https://www.ic3.gov/AnnualReport/Reports/)
- [CompTIA Security+ SY0-701 Exam Objectives](https://comptiacdn.azureedge.net/webcontent/docs/default-source/exam-objectives/comptia-security-sy0-701-exam-objectives-(6-0).pdf) (Domain 2.2)
