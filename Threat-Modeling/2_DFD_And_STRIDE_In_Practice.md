# DFD and STRIDE in practice

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

How to draw a useful model and walk STRIDE across it. Workshop habits: [1](./1_Workshop_Process.md). Literacy: [Foundations/12](../Foundations/12_STRIDE_And_Threat_Modeling.md).

## Concept

A **data flow diagram (DFD)** answers “what are we working on?” with pictures the whole room can argue about. For threat modeling, Microsoft-style DFDs use:

| Element | Meaning |
|---------|---------|
| **External entity / interactor** | Person, system, or service outside the process you own |
| **Process** | Running code / computation |
| **Data store** | Persistence (DB, bucket, queue, registry, secrets store) |
| **Data flow** | Data in motion between elements |
| **Trust boundary** | Where trust level changes (internet → edge → service → store; admin plane separate) |

Trust boundaries organize the diagram. **Attack surface** is the set of reachable entry points — inventory and shrink it.

```text
[User] --HTTPS--> [API] --private--> [Service] --private--> [DB]
                     |                    |
                trust boundary       trust boundary
```

Every arrow that crosses a boundary is a candidate for spoofing, tampering, disclosure, or denial of service.

## Advanced

### Drawing rules that keep workshops honest

| Rule | Why |
|------|-----|
| One scope per diagram | Nested systems get a child DFD |
| Name data classes on stores and sensitive flows | You cannot protect “stuff” |
| Mark assumptions explicitly | “IdP is honest” is an assumption — write it |
| Prefer ugly accurate over pretty wrong | Perfect representation is a Manifesto anti-pattern |
| Cloud / hybrid: draw shared-responsibility edges | Who patches the managed DB? Who configures IAM? |

**Data-centric lens (NIST SP 800-154 draft):** when the asset that matters is *data*, characterize where it is stored, transmitted, processed, input, and output — then attack and defend those locations. Treat SP 800-154 as draft guidance for principles, not a finished federal mandate.

### STRIDE as prompts (not a score)

| Category | Violates | Pointed question |
|----------|----------|------------------|
| **Spoofing** | Authentication | Can a caller forge identity here? |
| **Tampering** | Integrity | What integrity protection exists on this data or path? |
| **Repudiation** | Non-repudiation / accountability | Can we prove who did what? |
| **Information disclosure** | Confidentiality | Where can sensitive data leak? |
| **Denial of service** | Availability | What is the cheapest way to exhaust us? |
| **Elevation of privilege** | Authorization | Where can trust jump upward? |

### STRIDE-per-element habit

Walk the diagram element-by-element (or flow-by-flow). Ask which STRIDE categories apply to *this* process, store, or crossing.

| Building block | Example concerns |
|----------------|------------------|
| API gateway | Spoofed clients; missing access logs; cheap floods; exposed admin routes |
| App service | Stolen sessions; IDOR; over-fetched personal data |
| Queue | Spoofed producers; poisoned messages; secrets in dead-letter logs |
| Cache | Poisoned entries; secrets in keys/values |
| Database | Stolen credentials; unauthorized writes; over-broad roles |
| CI / deploy | Stolen deploy identity; malicious workflow; secrets in logs |
| Object store | Public ACL mistake; unencrypted sensitive objects |

### Ranking design threats

Use **likelihood × impact** (local scale) for design threats that have no CVE yet. Use **CVSS** when ranking known vulnerabilities — it is a severity companion, not a threat-modeling method ([Vulnerability-Management/3](../Vulnerability-Management/3_Risk_Based_Prioritization_KEV_EPSS_SSVC.md)). Optional awareness: older **DREAD** mnemonic — do not require it.

### Cloud adaptations

Shared responsibility, managed APIs, multi-tenant identity, and ephemeral infra (containers, serverless, IaC) change *where* trust boundaries sit — not whether you need them. Draw IAM roles, network edges, and data stores the customer still configures.

## Apply

1. Draw a one-page DFD for a system you own; mark every trust boundary.  
2. Label data classes on two stores and one egress flow.  
3. Run STRIDE on three boundary-crossing flows; write six concrete threats (one per letter if possible).  
4. Rank with likelihood × impact; pick mitigate vs accept for the top three.  
5. File tickets with owners ([5](./5_Tooling_And_Closing_The_Loop.md)).

### Self-check

- [ ] Name the five DFD building blocks used in threat modeling  
- [ ] Contrast trust boundary vs attack surface  
- [ ] Map each STRIDE letter to the property it violates  
- [ ] Explain why CVSS is not your design-threat ranking method  
- [ ] State one cloud-specific trust-boundary example  

## Next

- [PASTA staged walkthrough](./3_PASTA_Staged_Walkthrough.md)

## Further reading

- [Microsoft Learn — Threats (STRIDE)](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats)
- [Microsoft — SDL Threat Modeling](https://www.microsoft.com/en-us/securityengineering/sdl/threatmodeling)
- [OWASP — Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [NIST SP 800-154 (draft) — Data-Centric System Threat Modeling](https://csrc.nist.gov/pubs/sp/800/154/ipd)
- [SEI — Threat Modeling Methods Summary](https://insights.sei.cmu.edu/library/threat-modeling-a-summary-of-available-methods/)
