# PASTA staged walkthrough

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

When STRIDE-on-a-DFD is not enough and the business needs a risk-centric staged method. Workshop baseline: [1](./1_Workshop_Process.md). Diagram skills: [2](./2_DFD_And_STRIDE_In_Practice.md).

## Concept

**PASTA** (Process for Attack Simulation and Threat Analysis) is a **risk-centric** threat-modeling methodology. It connects business objectives and compliance needs to technical attack simulation and residual-risk decisions — heavier than a single STRIDE whiteboard, useful when impact and stakeholders demand it.

| STRIDE-on-DFD habit | PASTA habit |
|---------------------|-------------|
| Design-centric prompts on a diagram | Staged process from business objectives → residual risk |
| Fast for one service change | Deeper for high-stakes apps / programs |
| Security + eng often enough | Needs business / risk voices in early stages |

You still answer the four Manifesto questions; PASTA just sequences more evidence before “what will we do?”

## Advanced

### Seven stages (literacy map)

| Stage | Focus | Typical outputs |
|-------|-------|-----------------|
| **I — Define objectives** | Business goals; security objectives; compliance (e.g. PCI, privacy law); what data matters | Objectives + impact framing |
| **II — Define technical scope** | Boundaries; components; dependencies; attack surface edges | Scoped architecture / tech inventory |
| **III — Application decomposition** | Actors, assets, services, roles, data sources; **DFDs** and trust boundaries | Decomposition + DFDs |
| **IV — Threat analysis** | Likely scenarios from intel, monitoring, fraud patterns, known campaigns | Prioritized threat scenarios |
| **V — Vulnerability & weakness analysis** | Map threats to weaknesses (design flaws, known vulns, CWE/CVE-shaped evidence) | Threat↔weakness map |
| **VI — Attack modeling & simulation** | Attack surface; **attack trees**; how vulns chain into goals | Attack models / trees |
| **VII — Risk & impact analysis** | Business impact; control gaps; residual risk; mitigation strategy | Ranked risks + decisions |

```text
Objectives → Scope → Decompose (DFD)
  → Threats → Weaknesses → Attack models
    → Residual risk + mitigations
```

Stages IV–VI intentionally pull **threat intelligence** and real-world patterns into design work — complementary to [Foundations/19](../Foundations/19_Threat_Intelligence_Literacy.md), not a CTI deep dive (that lives in [Threat-Intelligence](../Threat-Intelligence/README.md) when filled).

### When to choose PASTA

| Choose PASTA when | Prefer lighter STRIDE workshop when |
|-------------------|-------------------------------------|
| Leadership needs business-impact narrative | Small design change, clear owners |
| Compliance / BIA must drive the model | Team is building the modeling muscle |
| Multi-team, multi-layer app with fraud/abuse | Single service, one trust-boundary change |
| You will simulate multi-step attacks | You need tickets this sprint |

Do not run full PASTA on every PR. Scope stages to manageable slices (Manifesto: align with iterative development).

### Relationship to other notes

| PASTA need | This handbook |
|------------|---------------|
| DFDs / trust boundaries | [2](./2_DFD_And_STRIDE_In_Practice.md) |
| Attack trees in stage VI | [4](./4_Attack_Trees_And_LINDDUN.md) |
| Vuln scoring companions | [Vulnerability-Management/3](../Vulnerability-Management/3_Risk_Based_Prioritization_KEV_EPSS_SSVC.md) |
| Accept residual risk | [Vulnerability-Management/4](../Vulnerability-Management/4_Exception_And_Acceptance_Workflows.md) |

## Apply

1. Pick one high-stakes system and write Stage I in one page: business objective, security objective, top data class, compliance hook.  
2. Draw Stage II/III scope: what’s in, what’s out, one DFD.  
3. List three Stage IV threat scenarios that are *plausible* (not movie plots).  
4. For one scenario, sketch a mini attack tree (Stage VI) and one Stage VII residual-risk decision.  
5. Compare effort to a 90-minute STRIDE workshop — document when you would escalate to PASTA next time.

### Self-check

- [ ] Expand PASTA and state why it is called risk-centric  
- [ ] Recite the seven stages in order  
- [ ] Explain when PASTA beats a STRIDE whiteboard  
- [ ] Name which stages consume DFDs and attack trees  
- [ ] Tie Stage VII acceptance to an owned exception habit  

## Next

- [Attack trees and LINDDUN](./4_Attack_Trees_And_LINDDUN.md)

## Further reading

- [SEI — Threat Modeling: A Summary of Available Methods](https://insights.sei.cmu.edu/library/threat-modeling-a-summary-of-available-methods/)
- [UW MIST — PASTA Threat Modeling Methodology (course chapter)](https://research.cs.wisc.edu/mist/SoftwareSecurityCourse/Chapters/08-PASTA-Threat-Modeling.pdf)
- [OWASP — Threat Modeling Cheat Sheet (method chooser)](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [OWASP AppSecEU 2012 — PASTA slides](https://wiki.owasp.org/images/a/aa/AppSecEU2012_PASTA.pdf)
