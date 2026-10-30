# Attack classes

[← Threats](./README.md)

> **Progression:** concept → advanced → apply.

Attacks are **actions** adversaries take. Vulnerabilities are **weaknesses**. Do not confuse the two. This note is the classical Domain 2.4 action catalog (physical, network, application, crypto, password). Malware-specific classes: [4](./4_Malware.md). AI/ML actions: [8](./8_AI_Era_Threats.md). Wire depth: [Networks-Deep-Dive Security](https://github.com/thisiskushal31/Networks-Deep-Dive). App depth: [AppSec](../AppSec/README.md).

## Concept

```text
Vulnerability (weakness)  ← exploited by ←  Attack (action)
                                    ↓
                              Impact / indicators
```

Security+ Domain 2.4 groups non-malware activity into **physical**, **network**, **application**, **cryptographic**, and **password** attacks.

## Advanced

### Physical attacks

| Attack | Definition |
|--------|------------|
| **Brute force (physical)** | Forcing entry through physical barriers (doors, locks, cages) |
| **RFID cloning** | Copying an RFID badge/token to impersonate authorized physical access |
| **Environmental** | Attacking availability/safety via power, HVAC, fire, flood, or similar facility conditions |

### Network attacks

| Attack | Definition |
|--------|------------|
| **Denial of service (DoS)** | Make a service unavailable or delayed for legitimate users |
| **Flooding DoS** | Overwhelm resources with incomplete or excessive requests |
| **Crash DoS** | Exploit a vulnerability to crash the target |
| **Distributed DoS (DDoS)** | DoS from many machines at once (coordinated actors or a botnet) — harder to source and more powerful |
| **Amplified DDoS** | Small queries trigger much larger responses toward the victim |
| **Reflected DDoS** | Spoof victim address so third parties send replies to the victim |
| **DNS attacks** | Abuse resolution (poisoning, hijacking, flooding DNS services) so names resolve wrong or DNS fails |
| **Wireless attacks** | Abuse Wi-Fi/Bluetooth (rogue AP / evil twin, weak crypto, eavesdropping) |
| **On-path (MitM)** | Attacker positioned between parties to intercept and/or alter data in transit |
| **Credential replay** | Reuse captured authentication material (tokens, hashes, cookies) to gain access |
| **Malicious code (on the wire)** | Delivery or command traffic carrying hostile payloads across the network |

```text
Normal:  Client  <-------->  Server

On-path: Client  <->  Attacker  <->  Server
                 (intercept / alter)
```

### Application attacks

| Attack | Definition |
|--------|------------|
| **Injection** | Hostile input reaches an interpreter (SQL, OS command, LDAP, template, etc.) |
| **Buffer overflow** | Overwrite memory via oversized input (see [3](./3_Vulnerabilities.md)) |
| **Replay** | Capture a valid message/transaction and resubmit it to repeat an effect |
| **Privilege escalation** | Move from lower to higher rights (vertical) or to another account at same level (horizontal) |
| **Forgery** | Forge a request or artifact the system trusts (for example cross-site request forgery patterns) |
| **Directory / path traversal** | Use `../` (and encodings) or absolute paths to read files outside the intended directory (web root) |

Path traversal is also called directory climbing / “dot-dot-slash”. Access is still bounded by OS permissions.

### Cryptographic attacks

| Attack | Definition |
|--------|------------|
| **Downgrade** | Force a weaker protocol/algorithm than the parties intended (for example TLS → obsolete SSL) |
| **Collision** | Find two different inputs with the same hash — breaks integrity assumptions for that hash |
| **Birthday attack** | Collision-finding approach based on birthday-paradox probability — fewer trials than naive brute force to find some colliding pair |

### Password attacks

| Attack | Definition |
|--------|------------|
| **Brute force** | Many password guesses against one (or few) accounts — lockouts often concentrate on that account |
| **Password spraying** | One (or few) common/default passwords tried across **many** accounts to avoid single-account lockouts |
| **Credential stuffing** (related) | Reuse username/password pairs leaked from other breaches against your login |

```text
Brute force:   many passwords  →  one account
Spraying:      one password    →  many accounts
```

Mitigations for spraying: lockout/CAPTCHA on failures, force change of defaults, MFA ([OWASP Password Spraying](https://owasp.org/www-community/attacks/Password_Spraying_Attack)).

### AI / ML attack classes (pointer)

Domain 2.4’s five buckets do not list AML/LLM techniques. Those live in [8](./8_AI_Era_Threats.md): poisoning, evasion, prompt injection, model extraction, excessive agency, and related GenAI risks. Keep this note for classical physical/network/app/crypto/password attacks.

### Real cases (these already happened)

Short examples from official public reports so you know this is not theory. Links have the full story — this is **not** a how-to.

NIST SP 800-61r3 lists concrete **incident shapes** defenders actually plan for — map them to this note’s classes:

| NIST-style incident example | Attack-class home here |
|-----------------------------|------------------------|
| Botnet flooding an internet-facing service | DDoS / availability |
| Phishing emails → compromised accounts → financial fraud | Social vector ([2](./2_Vectors_And_Surfaces.md)) + credential abuse / password attacks |
| Ransomware preventing use of systems **and** copying files | Malware ([4](./4_Malware.md)) + extortion pattern |
| New vulnerability in network appliances → unauthorized access | Network / exploit of public-facing gear ([3](./3_Vulnerabilities.md)) |
| Compromised vendor software distributed to customers | Supply-chain ([2](./2_Vectors_And_Surfaces.md) · [3](./3_Vulnerabilities.md)) |
| Intrusion used to impact industrial/control outcomes | Network + privilege path; resilience literacy in [Foundations/21](../Foundations/21_Resilience_Continuity_And_Recovery_Literacy.md) |

Password spraying and stuffing remain everyday login-facing patterns; treat them as **expected** pressure on any internet-exposed auth ([OWASP Password Spraying](https://owasp.org/www-community/attacks/Password_Spraying_Attack)).

## Apply

1. Name the attack **class** and specific technique.  
2. Name the **vulnerability or human weakness** it abuses.  
3. List **indicators** you would expect ([6](./6_Indicators_And_Analysis.md)).  
4. Pick **mitigations** ([7](./7_Mitigations.md)).  
5. For path traversal / injection / XSS, push the fix into AppSec secure coding — not only a WAF.

### Self-check

- [ ] Separate attack vs vulnerability with one example pair  
- [ ] Explain DDoS amplified vs reflected  
- [ ] Define on-path using the NIST positioning idea  
- [ ] Define directory traversal without writing an exploit  
- [ ] Contrast spraying, brute force, and credential stuffing  
- [ ] Contrast collision vs birthday attack  

## Next

- [Indicators and analysis](./6_Indicators_And_Analysis.md)

## Further reading

- [NIST glossary — man-in-the-middle attack](https://csrc.nist.gov/glossary/term/man_in_the_middle_attack)
- [OWASP Attacks index](https://owasp.org/www-community/attacks/)
- [OWASP Path Traversal](https://owasp.org/www-community/attacks/Path_Traversal)
- [OWASP Password Spraying](https://owasp.org/www-community/attacks/Password_Spraying_Attack)
- [NIST SP 800-61r3 — Incident Response Recommendations](https://csrc.nist.gov/pubs/sp/800/61/r3/final) (incident examples)
- [Canadian Centre for Cyber Security — Introduction to the cyber threat environment](https://www.cyber.gc.ca/en/guidance/introduction-cyber-threat-environment)
- [CompTIA Security+ SY0-701 Exam Objectives](https://comptiacdn.azureedge.net/webcontent/docs/default-source/exam-objectives/comptia-security-sy0-701-exam-objectives-(6-0).pdf) (Domain 2.4)
