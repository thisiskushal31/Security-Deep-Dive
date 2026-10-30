# PASTA staged walkthrough

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

Risk-centric threat modeling when a STRIDE whiteboard is not enough — seven stages, inputs/outputs, and how each stage consumes earlier handbook skills. DFD/STRIDE: [2](./2_DFD_And_STRIDE_In_Practice.md). Attack trees: [4](./4_Attack_Trees_And_LINDDUN.md).

## Concept

**PASTA** (Process for Attack Simulation and Threat Analysis) is a **risk-centric** methodology (UcedaVélez / Morana lineage; summarized by SEI and teaching materials). It deliberately connects **business objectives and compliance** to **technical attack simulation** and **residual-risk decisions**.

| Dimension | STRIDE-on-DFD workshop | PASTA |
|-----------|------------------------|-------|
| Center of gravity | Design / software structure | Business risk + attacker simulation |
| Typical duration | Hours | Days to weeks (scoped slices) |
| Primary consumers | Eng + security | Eng + security + business / risk / compliance |
| Ending artifact | Mitigated design threats | Prioritized business impact + residual risk plan |

PASTA still answers the four Manifesto questions; it spends more stages gathering evidence before “what will we do?”

You do **not** run full PASTA on every pull request. Use it when impact, fraud, regulation, or multi-team complexity demands a narrative leadership will fund.

## Advanced

### Seven stages — full literacy map

SEI’s stage map (activities condensed for handbook use):

#### Stage I — Define objectives

| Activity | Output |
|----------|--------|
| Identify business objectives | Why the app exists; what “win” means |
| Identify security & compliance requirements | HIPAA / PCI / privacy law / internal policy hooks |
| Business impact analysis framing | What loss of CIA (or privacy) costs the mission |

This is broader than “draw boxes.” If Stage I is vague, later stages produce theater.

#### Stage II — Define technical scope

| Activity | Output |
|----------|--------|
| Capture technical environment boundaries | What’s in / out of the model |
| Inventory infrastructure, application, software dependencies | Attack surface edges and trust you inherit |
| Note components that *provide* security controls | Authn, encryption, WAF — and who owns them |

#### Stage III — Application decomposition & analysis

| Activity | Output |
|----------|--------|
| Use cases; entry points; trust levels | Who can touch what |
| Actors, assets, services, roles, data sources | Asset/actor list |
| **DFDs and trust boundaries** | Same craft as [2](./2_DFD_And_STRIDE_In_Practice.md) |

If you cannot produce a good DFD, stop and fix Stage III before “threat intel theater” in Stage IV.

#### Stage IV — Threat analysis

| Activity | Output |
|----------|--------|
| Enumerate threats against the application as an asset | Threat list |
| Probable scenarios from monitoring, fraud, intel, known campaigns | Prioritized scenarios (not movie plots) |
| Regression on security events | “What already happened to us / peers?” |

PASTA leans on **demonstrated** threats. Advantage: realism. Limitation: unique design flaws may be under-weighted — extend with STRIDE on the DFD so novel design abuse is not ignored (UW PASTA teaching note).

#### Stage V — Vulnerability & weakness analysis

| Activity | Output |
|----------|--------|
| Map threats to existing vulns / design flaws | Threat↔weakness matrix |
| Use CWE/CVE, issue trackers, abuse cases | Evidence beyond opinion |
| Optional scoring companions (CVSS/CWSS) | Severity language for known defects |

This stage bridges design TM and [Vulnerability-Management](../Vulnerability-Management/README.md). Design flaws without CVEs still count.

#### Stage VI — Attack modeling & simulation

| Activity | Output |
|----------|--------|
| Attack surface analysis | Where paths concentrate |
| **Attack trees** / attack libraries | How vulns chain to goals ([4](./4_Attack_Trees_And_LINDDUN.md)) |
| Map attacks to vulnerabilities | Exploitability narrative (authorized analysis only) |

#### Stage VII — Risk & impact analysis / management

| Activity | Output |
|----------|--------|
| Qualify/quantify business impact | Why leadership should care |
| Control gaps + residual risk | Honest leftover risk |
| Mitigation strategies | Funded decisions; accept with expiry where needed |

```text
I Objectives → II Scope → III Decompose (DFD)
     → IV Threats → V Weaknesses → VI Attack models
          → VII Residual risk + mitigations
```

### Stage-by-stage “definition of done”

| Stage | Done when… |
|-------|------------|
| I | One-page objectives + data classes + compliance hooks signed by owner |
| II | In/out list + dependency sketch agreed |
| III | DFD passes hygiene rules in [2](./2_DFD_And_STRIDE_In_Practice.md) |
| IV | ≤10 prioritized scenarios with evidence notes |
| V | Each top scenario maps to ≥1 weakness or “design gap” |
| VI | At least one attack tree for the #1 scenario |
| VII | Every top risk has mitigate/transfer/accept + owner |

### Mapping PASTA to Microsoft SDL / four questions

| PASTA | SDL / four questions |
|-------|----------------------|
| I–II | Security requirements + scope (Q1 prelude) |
| III | Diagram (Q1) |
| IV–VI | Identify (+ simulate) (Q2) |
| VII | Mitigate + residual decisions (Q3) |
| Revisit after change | Validate (Q4) |

### When PASTA vs STRIDE vs OCTAVE vs VAST

| Need | Prefer |
|------|--------|
| One service design change | STRIDE workshop ([1](./1_Workshop_Process.md)–[2](./2_DFD_And_STRIDE_In_Practice.md)) |
| Business-impact narrative + attack simulation | **PASTA** |
| Org-wide critical asset risk workshops | OCTAVE (enterprise; then zoom apps with STRIDE) |
| Many agile teams needing light continuous models | VAST-style app + ops models in the workflow |
| Privacy harms | LINDDUN ([4](./4_Attack_Trees_And_LINDDUN.md)) — can run beside PASTA Stages III–VII |

### Anti-patterns specific to PASTA

| Anti-pattern | Better |
|--------------|--------|
| Full seven stages on a tiny CRUD change | STRIDE checklist |
| Stage IV threat theater without Stage III DFD | Fix decomposition first |
| Skipping Stage VII acceptance paperwork | Owned residual risk or it did not happen |
| Treating PASTA book length as required reading to start | Use this stage map; deepen with SEI/course PDFs as needed |
| Using PASTA to rediscover baseline ASVS controls | Map Stage V/VII to ASVS; spend PASTA energy on business-logic and novel paths ([5](./5_Tooling_And_Closing_The_Loop.md)) |
| Stage IV scenarios with no assumptions about attacker capability | Write “secure against X given Y” before ranking |

## Apply

1. Choose one high-stakes system. Write Stage I in ≤1 page.  
2. Produce Stage II in/out + Stage III DFD (reuse [2](./2_DFD_And_STRIDE_In_Practice.md) hygiene).  
3. List five Stage IV scenarios; mark evidence source (intel, past incident, fraud pattern, STRIDE finding).  
4. For the top scenario: Stage V weakness map + Stage VI mini attack tree.  
5. Stage VII: three decisions with owners; any acceptances get expiry.  
6. Write a two-sentence rule: “We escalate from STRIDE to PASTA when ___.”

### Self-check

- [ ] Expand PASTA and explain “risk-centric” without saying “more steps”  
- [ ] Recite all seven stages and one output each  
- [ ] Explain the demonstrated-threat strength and novel-design weakness of Stage IV  
- [ ] Map Stages III and VI to other notes in this folder  
- [ ] State a clear escalate-to-PASTA rule for your org  

## Next

- [Attack trees and LINDDUN](./4_Attack_Trees_And_LINDDUN.md)

## Further reading

- [SEI — Threat Modeling: A Summary of Available Methods](https://insights.sei.cmu.edu/library/threat-modeling-a-summary-of-available-methods/)
- [UW MIST — PASTA Threat Modeling Methodology (course chapter)](https://research.cs.wisc.edu/mist/SoftwareSecurityCourse/Chapters/08-PASTA-Threat-Modeling.pdf)
- [OWASP AppSecEU 2012 — PASTA slides](https://wiki.owasp.org/images/a/aa/AppSecEU2012_PASTA.pdf)
- [OWASP — Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
