# Mitigations

[← Threats](./README.md)

> **Progression:** concept → advanced → apply.

Purpose of each enterprise mitigation technique in Security+ Domain 2.5 — what it does and when to reach for it. After you can name the threat path, this note answers **what reduces likelihood or impact**. Control types: [Foundations/2](../Foundations/2_Controls_And_Physical.md). Product depth lives in AppSec, Identity, Cloud-Security, Defensive-Ops, Networks, and Handbook gates.

## Concept

A **mitigation** reduces likelihood or impact of a threat event exploiting a vulnerability. One risk usually needs several families.

```text
Scenario (actor + vector + vuln + asset)
        → preventive + detective + corrective
        → owner + review date
```

| Technique | Purpose |
|-----------|---------|
| **Segmentation** | Limit how far an attacker can move after a foothold (network, identity, account, namespace) |
| **Access control** | Enforce who/what may access resources (**ACLs**, **permissions**) |
| **Least privilege** | Grant only rights required for the job — people and workloads |
| **Application allow list** | Allow only authorized executables/scripts; block the rest |
| **Isolation** | Separate risky workloads (sandbox, VLAN/VPC, quarantine infected hosts) |
| **Patching** | Remove known vulnerabilities on a risk-informed schedule |
| **Encryption** | Protect confidentiality/integrity of data in transit and at rest (including backups) |
| **Monitoring** | Detect anomalies and known-bad activity; alert humans who can act |
| **Configuration enforcement** | Keep systems on approved baselines; detect drift |
| **Decommissioning** | Retire unused systems so they stop being attack surface |

## Advanced

### Hardening techniques (explicit list)

| Technique | Purpose |
|-----------|---------|
| **Encryption** (host/disk) | Protect data if device is lost or stolen |
| **Endpoint protection / EDR** | Detect/contain host malice; escalate to responders |
| **Host-based firewall** | Restrict local listeners and lateral paths |
| **HIPS** | Host intrusion prevention — block known-bad host behaviors where deployed |
| **Disable ports/protocols** | Remove unused network attack surface (including unused RDP/FTP/SMB exposure) |
| **Default password changes** | Eliminate the easiest credential vector |
| **Remove unnecessary software** | Fewer vulnerabilities and fewer execution paths |

Prefer **allowlisting** over endless blocklists when the environment supports it. Keep anti-malware centrally managed with current signatures — ransomware often follows precursor malware.

### Mitigations by common paths (practice patterns)

| Path | Mitigations that matter |
|------|-------------------------|
| **Phishing / email** | User reporting; external-mail flags; gateway filters; attachment controls; DMARC/SPF/DKIM; disable internet macros; phishing-resistant MFA |
| **Password spray / brute force** | Lockout/CAPTCHA; MFA; ban defaults; force password change on first login; monitor failure patterns ([5](./5_Attack_Classes.md)) |
| **Exposed remote services** | Close unused ports; MFA; account lockouts; logging; put essential remote access behind securely configured VPN/firewall |
| **Valid account abuse** | MFA; least privilege; rapid revoke; session anomaly detection ([6](./6_Indicators_And_Analysis.md)) |
| **Precursor → ransomware** | EDR/allow list; patch; segment; offline/segmented known-good backups; monitor unusual scripting and account changes |
| **Supply chain / MSP** | Least privilege for vendors; monitoring; contracts; signed artifacts / SCA |
| **Injection / XSS / traversal** | Secure coding; parameterized queries; input validation; WAF as defense-in-depth ([AppSec](../AppSec/README.md)) |
| **DDoS / availability** | Capacity, filtering, CDN/scrubbing where appropriate; rate limits ([Networks](https://github.com/thisiskushal31/Networks-Deep-Dive)) |
| **Deepfake / voice-clone fraud** | Out-of-band callback; dual control for money/access; synthetic-media awareness ([8](./8_AI_Era_Threats.md)) |
| **LLM app / agent** | Least-privilege tools; human approval + kill switch; treat retrieved/tool content as untrusted; validate model output; rate/cost limits; AIBOM ([8](./8_AI_Era_Threats.md) · [AI-Security](../AI-Security/README.md)) |
| **Shadow AI / public GenAI** | Acceptable-use policy; allow/deny lists; no secrets in prompts; private/approved instances ([8](./8_AI_Era_Threats.md)) |

### Backups and IR as mitigations

Maintain regular **known-good** backups stored **offline** or otherwise segmented from production. Many ransomware variants try to encrypt or delete online backups. **Test restores.** Victims with intact backups often recover without paying.

Exercise a basic cyber incident response plan and communications plan — the ability to respond is part of mitigation ([Defensive-Ops/2](../Defensive-Ops/2_Incident_Response.md) · [Foundations/21](../Foundations/21_Resilience_Continuity_And_Recovery_Literacy.md)).

### Mapping back across Threats

| Note | Mitigation emphasis |
|------|---------------------|
| [1](./1_Actors_And_Motivations.md) Actors | Match controls to motivation (money vs espionage vs disruption) |
| [2](./2_Vectors_And_Surfaces.md) Vectors | Shrink surface; harden each entry path |
| [3](./3_Vulnerabilities.md) Vulns | Patch, config enforcement, KEV priority |
| [4](./4_Malware.md) Malware | EDR, allow list, backups, IR |
| [5](./5_Attack_Classes.md) Attacks | Technique-specific controls in table above |
| [6](./6_Indicators_And_Analysis.md) Indicators | Monitoring + correlation + response criteria |
| [8](./8_AI_Era_Threats.md) AI-era | Out-of-band verify; agent least privilege; output validation; model/data supply chain; AI Secure by Design |

### Where implementation depth lives

| Topic | Home |
|-------|------|
| PR→prod gate chain | [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security) |
| App mitigations | [AppSec](../AppSec/README.md) |
| LLM / agent depth | [AI-Security](../AI-Security/README.md) |
| Identity / Zero Trust | [Identity](../Identity/README.md) |
| Detection / IR | [Defensive-Ops](../Defensive-Ops/README.md) |
| Cloud posture | [Cloud-Security](../Cloud-Security/README.md) |
| Wire / perimeter | [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive) |

### Real cases (these already happened)

Short examples from official public reports so you know this is not theory. Links have the full story — this is **not** a how-to.

These cases are why the same controls keep showing up in guidance:

| What kept going wrong | Controls that official guidance repeats | Where to read more |
|------------------------------|----------------------------------------------------|--------|
| Ransomware on IT disrupting critical business | Segmentation (esp. IT/OT), MFA on remote access, offline/tested backups, spam/phish filters | [CISA/FBI AA21-131a](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-131a) · [#StopRansomware](https://www.cisa.gov/stopransomware/ransomware-guide) |
| Wormable malware on unpatched fleets | Patching + host firewall / lateral movement limits | WannaCry / NotPetya era CISA alerts · [4](./4_Malware.md) |
| Mass exploitation of known vulns | KEV-informed patch SLA; internet-facing first | [CISA KEV](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) · Log4Shell guidance |
| BEC / AI-enhanced social engineering | Out-of-band verify; phishing-resistant MFA; dual control for money | IC3 BEC · [8](./8_AI_Era_Threats.md) · [2](./2_Vectors_And_Surfaces.md) |

## Apply

1. State the scenario (actor, vector, vuln, asset).  
2. Pick at least one **preventive**, one **detective**, and one **corrective** control.  
3. Assign **owner** and **review date**.  
4. For ransomware-class risk, verify **offline/segmented backups** and a tested restore.  
5. Give compensating controls an **expiry**.

### Self-check

- [ ] Recite all Domain 2.5 mitigation techniques and their purpose  
- [ ] List every hardening technique in the exam list  
- [ ] For phishing→ransomware, name preventive, detective, and corrective controls  
- [ ] Explain why online-only backups are weak against ransomware  
- [ ] Contrast allowlisting vs blocklisting  
- [ ] Name one CI ransomware advisory takeaway (segmentation / backups / MFA)  

## Next

- [AI-era threats](./8_AI_Era_Threats.md) → [AppSec](../AppSec/README.md) · [AI-Security](../AI-Security/README.md) · [Defensive-Ops](../Defensive-Ops/README.md) · [Offensive](../Offensive/README.md) (lab) · [GRC](../GRC/README.md)

## Further reading

- [CISA — #StopRansomware Guide](https://www.cisa.gov/stopransomware/ransomware-guide)
- [CISA/FBI — DarkSide CI advisory (AA21-131a)](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-131a)
- [CISA — Cross-Sector Cybersecurity Performance Goals](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals)
- [NIST SP 800-53 Rev. 5](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final)
- [CIS Critical Security Controls](https://www.cisecurity.org/controls)
- [CompTIA Security+ SY0-701 Exam Objectives](https://comptiacdn.azureedge.net/webcontent/docs/default-source/exam-objectives/comptia-security-sy0-701-exam-objectives-(6-0).pdf) (Domain 2.5)
