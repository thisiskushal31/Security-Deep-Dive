# Secure design principles

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

These principles show up in almost every secure system. They complement control types ([2](./2_Controls_And_Physical.md)), access models ([7](./7_AAA_And_Access_Fundamentals.md)), and classic security models ([11](./11_Security_Models_And_Architecture_Literacy.md)). Product inception and SDLC application continue in [AppSec](../AppSec/README.md).

## Concept

## Core principles

| Principle | Meaning |
|-----------|---------|
| **Security by design** | Treat security requirements as first-class design inputs — not a late add-on |
| **Secure by default** | Default configuration is restrictive; opening capability is deliberate |
| **No absolute guarantee** | Given enough time and resources, any system can be compromised; make attacks hard and rewards low |
| **Defense in depth** | Multiple independent layers so one failure is not total loss |
| **Fail secure** | On error, prefer a safe state (typically deny) rather than an open state |
| **Least privilege** | Minimum rights, for the minimum time, for people and processes |
| **Compartmentalize / need to know** | Limit access to what a task requires so breach blast radius shrinks |
| **Separation of duties** | Critical tasks need more than one condition or person |
| **Economy of mechanism** | Prefer the simplest design that meets the requirement |
| **Complete mediation** | Check authorization on every access, not only at login |
| **Open design** | Security does not depend on hiding the design; secrets stay in keys and configs |
| **Least common mechanism** | Avoid shared mechanisms across privilege levels that enable escalation |
| **Psychological acceptability** | Controls users will actually use; excessive friction creates bypasses |
| **Usability and manageability** | Operators must be able to configure and run controls without obscure rituals |
| **Secure the weakest link** | Find and raise the lowest bar first |
| **Leverage existing components** | Reuse tried components carefully so you do not invent new attack surface |
| **Minimize attack surface** | Disable unused ports, features, and trust paths |
| **Zero Trust (strategy)** | No implicit trust from network location alone — [13](./13_Zero_Trust_And_Deception_Basics.md) |

```text
Requirements → secure defaults → least privilege
            → layered controls → fail secure
            → mediate every access → monitor
```

## Advanced

These principles compose; the next section turns them into a builder habit.

## Apply
1. Write data class and trust boundaries before features freeze ([12](./12_STRIDE_And_Threat_Modeling.md)).  
2. Ship closed defaults; document every exception with owner and expiry ([9](./9_Change_Management_And_Security.md)).  
3. Prefer boring, reviewed libraries and platform TLS over custom crypto ([8](./8_Cryptographic_Solutions_Literacy.md)).  
4. Make authorization checks local to the resource owner (complete mediation).  
5. Assume one layer fails; plan detect and recover ([2](./2_Controls_And_Physical.md)).

## Self-check

- [ ] Explain secure by default vs usable-but-open defaults  
- [ ] Give one example of complete mediation vs “auth once”  
- [ ] Name three principles that reduce blast radius  

## Next

- [Security models and architecture literacy](./11_Security_Models_And_Architecture_Literacy.md)

## Further reading

- [OWASP Developer Guide — Principles of security](https://devguide.owasp.org/en/02-foundations/03-security-principles/)
- [OWASP Secure Product Design Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secure_Product_Design_Cheat_Sheet.html)
- [NIST SP 800-160 Vol. 1 Rev. 1 — Engineering Trustworthy Secure Systems](https://csrc.nist.gov/pubs/sp/800/160/v1/r1/final)
