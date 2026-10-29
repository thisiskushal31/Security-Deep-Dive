# CIA and risk

[← Foundations](./README.md)

Language every security conversation starts with: what you protect, and how you decide what is worth protecting.

## CIA triad

| Letter | Meaning | Everyday example |
|--------|---------|------------------|
| **C**onfidentiality | Only authorized parties see the data | Encrypted DB column; access controlled API |
| **I**ntegrity | Data and systems are not altered unnoticed | Signed artifacts; checksums; immutable logs |
| **A**vailability | Authorized users can use the system when needed | Redundancy, rate limits, DDoS capacity |

Many programs add **authenticity** (you know who sent it) and **non-repudiation** (sender cannot credibly deny it). Treat those as integrity/identity cousins, not a second triad.

**Trade-off:** turning everything “most confidential” can hurt availability (locked-down systems nobody can operate). Risk work balances the three for *your* data class.

## Risk vs threat vs vulnerability vs control

| Term | Meaning |
|------|---------|
| **Asset** | Something of value (data, service, reputation, money) |
| **Threat** | Something that could harm an asset (actor or event) |
| **Vulnerability** | Weakness that a threat can use |
| **Risk** | Likelihood × impact that a threat exploits a vulnerability against an asset |
| **Control** | Measure that reduces likelihood or impact |

**Formula (mental model):** risk falls when you remove the vuln, block the threat path, or shrink impact (backups, blast-radius limits).

## How to talk about risk at work

1. Name the **asset** and data class (public / internal / confidential / restricted).
2. Name the **threat** (who or what) and the **path** (how).
3. Estimate **likelihood** and **impact** in plain language (or your org’s scale).
4. Pick **controls** with an owner and a review date.
5. Accept residual risk **on purpose** with expiry — or treat “accepted forever” as policy debt.

Formal registers and audit evidence: [GRC](../GRC/README.md). Design-time STRIDE: [2](./2_STRIDE_And_Threat_Modeling.md).

## Related home (do not rewrite here)

- Wire / TLS / firewalls → [Networks-Deep-Dive Security](https://github.com/thisiskushal31/Networks-Deep-Dive)
- Pipeline gates, secrets in CI → [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security)
- Design-time trade-offs → [System-Design Security-Tradeoffs](https://github.com/thisiskushal31/System-Design-Concepts/tree/main/Security-Tradeoffs)

## Next

- [STRIDE and threat modeling](./2_STRIDE_And_Threat_Modeling.md)
- [Controls and physical](./3_Controls_And_Physical.md)
