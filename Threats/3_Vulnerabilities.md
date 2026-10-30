# Vulnerabilities

[← Threats](./README.md)

> **Progression:** concept → advanced → apply.

Weaknesses that threat events can exploit — every major class named in Security+ Domain 2.3, with enough definition to triage. Vectors deliver; **vulnerabilities** are what make delivery succeed. Program loop: [Foundations/20](../Foundations/20_Vulnerability_Management_Literacy.md). App depth: [AppSec](../AppSec/README.md).

## Concept

A **vulnerability** is a weakness in an information system, security procedures, internal controls, or implementation that could be exploited by a threat source. Severity is **context-dependent**: impact if exploited in *your* environment matters as much as a generic score.

| Term | Meaning |
|------|---------|
| Exploit | Code or method that takes advantage of a vulnerability |
| Exploit kit | Collection of exploits that probes a visitor’s software and deploys a matching exploit |
| Payload | What runs after successful exploitation (access, control, destruction, further malware) |
| CVE | Public identifier for a known vulnerability |
| CVSS | Shared severity score — still needs exposure and asset context |
| CWE | Weakness *class* (design/coding pattern) |
| Zero-day vulnerability | Not yet known (or not yet patched) by the vendor |
| Zero-day exploit | Attack aimed at a zero-day vulnerability; once patched, it is no longer “zero-day” |
| Predisposing condition | Condition that increases likelihood of adverse impact if a threat event occurs |

![Malware attack path concept](../Assets/Threats/GFG_Malware_Attack.webp)

*Image: [Malware and its types](https://www.geeksforgeeks.org/ethical-hacking/malware-and-its-types/).*

```text
Vulnerability (+ predisposing condition)
        → threat event can reach it?
        → impact if successful?
                ↓
        remediate / mitigate / accept
```

## Advanced

### Application vulnerabilities

| Class | Definition |
|-------|------------|
| **Memory injection** | Hostile data or code introduced into a process’s memory space |
| **Buffer overflow** | Program writes more data than a buffer can hold, or past the buffer — can corrupt data, crash, or enable malicious code execution |
| **Race condition** | Behavior depends on uncontrolled timing of concurrent events; hard to detect; can leave inconsistent state |
| **TOC / TOU (TOCTOU)** | Time-of-check / time-of-use: a check passes, then the state changes before the action — classic race subtype (also called TOCTOU) |
| **Malicious update** | Update mechanism or package delivers hostile content |

### Web-based vulnerabilities

| Class | Definition |
|-------|------------|
| **SQL injection (SQLi)** | Untrusted input reaches a SQL interpreter so the attacker can read or modify database contents (often via forms meant for normal queries) |
| **Cross-site scripting (XSS)** | Malicious code injected into a web application and executed in users’ browsers by bypassing input validation — one-time or as a stepping stone |

![SQL injection concept](../Assets/Threats/GFG_SQL_Injection.webp)

*Image: [SQL Injection](https://www.geeksforgeeks.org/ethical-hacking/sql-injection/).*

```text
Untrusted input ──X──> SQL / HTML / shell / template sink
                 └──> validate · parameterize · encode
```

Injection broadly: hostile data used so it subverts the application. Prefer parameterized queries and safe APIs over concatenating untrusted strings into interpreters.

### Operating system, hardware, virtualization, cloud, mobile

| Class | Definition |
|-------|------------|
| **OS-based** | Weaknesses in the operating system (unpatched services, permissions, kernel issues) |
| **Firmware** | Weaknesses in device firmware below the main OS |
| **End-of-life / legacy** | Products without security fixes remaining |
| **VM escape** | Break out of a virtual machine to affect the host or other VMs |
| **Resource reuse** | Residual data or state from a previous tenant/VM/process becomes visible to the next — isolation failure |
| **Cloud-specific** | Weaknesses unique to cloud shared-responsibility models (for example public storage, over-broad IAM, exposed management planes) |
| **Side loading** | Installing apps from outside the official store/channel — raises malware risk |
| **Jailbreaking** (or rooting) | Removing platform restrictions — weakens built-in security controls |

### Supply chain, cryptographic, misconfiguration, zero-day

| Class | Definition |
|-------|------------|
| **Service provider** | Inherited weakness via a hosted/managed service |
| **Hardware provider** | Inherited weakness via hardware/OEM supply |
| **Software provider** | Inherited weakness via vendor software or libraries |
| **Cryptographic** | Weak algorithms/protocols, bad key handling, failed certificate validation, predictable randomness |
| **Misconfiguration** | Unsafe settings — defaults, open shares, debug, overly broad firewall rules. **No CVE required** |
| **Zero-day** | No vendor fix available yet — compensate (isolate, virtual patch, disable feature, extra monitoring) until patched |

### Prioritization companions

| Input | Use |
|-------|-----|
| CVSS | Shared severity language |
| Exposure | Internet-facing vs internal |
| Asset criticality / data class | Business impact |
| Known exploited catalogs | Issues actively abused in the wild often jump the queue |

## Apply

1. Classify the finding using the tables above.  
2. Score with CVSS **and** exposure, asset value, and known-exploited status.  
3. Choose remediate / mitigate / accept with owner and date ([Foundations/20](../Foundations/20_Vulnerability_Management_Literacy.md)).  
4. Verify with evidence (rescan/test).  
5. Feed recurring classes (injection, misconfig, TOC/TOU) into design reviews ([AppSec](../AppSec/README.md)).

### Self-check

- [ ] Define vulnerability, exploit, exploit kit, payload, zero-day  
- [ ] Explain buffer overflow and TOCTOU  
- [ ] Contrast SQLi vs XSS (database vs browser)  
- [ ] Define VM escape and resource reuse  
- [ ] Explain why misconfiguration belongs beside CVEs  

### Real cases (these already happened)

Short examples from official public reports so you know this is not theory. Links have the full story — this is **not** a how-to.

| What happened | Why it matters here | Where to read more |
|-----------------------|------------------------------|--------------------|
| **Log4Shell (CVE-2021-44228)** | Ubiquitous library + remote code execution + **active mass exploitation** — why “dependency inventory” and KEV matter | [CISA — Apache Log4j guidance](https://www.cisa.gov/news-events/news/apache-log4j-vulnerability-guidance) · [AA21-356a](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-356a) |
| **Known Exploited Vulnerabilities (KEV)** | Not every CVE is equal: CISA maintains evidence of **exploitation in the wild** as a patch-priority input | [CISA KEV Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog) |
| **Unpatched SMB-class flaws → wormable malware** | Vulnerability + network exposure enabled 2017 global ransomware/wiper waves | [CISA WannaCry / NotPetya alerts](https://www.cisa.gov/stopransomware/official-alerts-statements-cisa) · note [4](./4_Malware.md) |
| **Supply-chain code in a signed product** | “Malicious update” and vendor trust as vulnerability classes, not only missing patches | [CISA AA20-352a](https://www.cisa.gov/news-events/cybersecurity-advisories/aa20-352a) |

AI/ML-specific weakness classes (poisoning, prompt injection, model extraction, and related): [8](./8_AI_Era_Threats.md). Keep treating the AI *host stack* with the CVE/config rows above.

## Next

- [Malware](./4_Malware.md)

## Further reading

- [NVD](https://nvd.nist.gov/) · [FIRST CVSS](https://www.first.org/cvss/) · [CWE](https://cwe.mitre.org/)
- [CISA KEV Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
- [CISA — Apache Log4j Vulnerability Guidance](https://www.cisa.gov/news-events/news/apache-log4j-vulnerability-guidance)
- [OWASP Top Ten](https://owasp.org/www-project-top-ten/)
- [OWASP Buffer Overflow](https://owasp.org/www-community/vulnerabilities/Buffer_Overflow) · [OWASP Race Conditions](https://owasp.org/www-community/vulnerabilities/Race_Conditions)
- [NIST SP 800-30 Rev. 1](https://csrc.nist.gov/pubs/sp/800/30/r1/final)
- [CompTIA Security+ SY0-701 Exam Objectives](https://comptiacdn.azureedge.net/webcontent/docs/default-source/exam-objectives/comptia-security-sy0-701-exam-objectives-(6-0).pdf) (Domain 2.3)
