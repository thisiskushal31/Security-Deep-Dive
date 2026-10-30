# Tooling and closing the loop

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

How tools support the four questions — and how mitigations become tickets, tests, and revisit habits. Workshops: [1](./1_Workshop_Process.md). Diagrams: [2](./2_DFD_And_STRIDE_In_Practice.md).

## Concept

A model that never changes a design or a test failed question 4. Closing the loop:

| Output | Must land in |
|--------|----------------|
| Mitigations | Backlog / security requirements with owners |
| Accepted risks | Time-boxed exceptions |
| Assumptions | Design home + triggers |
| Validation | Tests and gates that can fail |

Tools accelerate drawing, STRIDE prompts, reporting, and sharing. They do not replace facilitation, diagram honesty, or ownership ([Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/): people over tools; useful toolkit, not worship).

Microsoft’s SDL tool pitch in one line: help non-security experts communicate design, analyze with a proven methodology, and manage mitigations — ideally plugged into the same issue tracker the team already uses.

### ASVS architecture expectations (verification target)

OWASP **ASVS** (v4 Architecture / Design / Threat Modeling family) treats threat modeling as something you can *verify*, not only admire:

| Idea (ASVS V1.1.x family) | Practice |
|---------------------------|----------|
| Threat model on design change / sprint planning | Countermeasures, risk responses, guide security testing |
| User stories carry functional security constraints | “I can edit *my* profile — not yours” |
| Document trust boundaries, components, significant flows | Your DFD + justification is evidence |
| Analyze high-level architecture and connected remote services | Partners and IdPs are in scope |
| Centralize vetted reusable security controls | Economy of design — fewer bespoke crypto paths |

Map mitigations to ASVS (or MASVS for mobile) when you need testable requirements. **Cornucopia** cards are built to accelerate that mapping into Agile stories ([1](./1_Workshop_Process.md)).

## Advanced

### Tooling landscape (literacy deep enough to choose)

| Approach | Strengths | Watch-outs |
|----------|-----------|------------|
| **Whiteboard + design doc** | Fastest; best for early argument | Easy to lose; hard to diff |
| **OWASP Threat Dragon** | Open; diagrams + threats together; STRIDE/LINDDUN/CIA; GitHub-friendly | Still needs process discipline |
| **Microsoft Threat Modeling Tool (TMT)** | SDL templates; STRIDE analysis view; Azure stencil packs; report export | Desktop/process fit varies by org |
| **pytm (OWASP)** | Model-as-Python; diagrams/reports as outputs; CI-native | Visual reviewers need generated DFD |
| **Threagile** | YAML model + rule engine; CI-friendly risk report | Schema learning curve |
| **ThreatSpec** (awareness) | Threats annotated near code | Comment hygiene; less expressive than pytm |

**Generated threats are prompts, not completeness.** Always add **custom** threats the template missed (business logic, your fraud paths). Status `Not Applicable` still needs a written reason.

**TMT workflow literacy (Microsoft getting-started):**

1. Pick a template (generic SDL knowledge base vs Azure template with cloud stencils/threats)  
2. Draw interactors, processes, stores, flows, trust boundaries  
3. Open **analysis view** — generated STRIDE candidates from the template  
4. For each threat: read description/mitigation guidance; set priority; status (`Not Started` / `Needs Investigation` / `Mitigated` / `Not Applicable`); write justification notes  
5. Create full report; share via repo/OneDrive; check into source control after sign-off  

Generated threats are prompts — not automatic truth. “Not Applicable” requires a written reason (existing control or out of scope).

### Threat record schema (tool-agnostic)

Every tracked threat should carry:

| Field | Purpose |
|-------|---------|
| ID | Stable link from tickets |
| Diagram element / interaction | Traceability to Q1 |
| Category | STRIDE / LINDDUN / tree-leaf ref |
| Statement | What can go wrong, in one sentence |
| Ranking | Likelihood × impact (or local bug bar) |
| Response | Mitigate / eliminate / transfer / accept |
| Mitigation / justification | How or why |
| Owner | Named |
| Status | Lifecycle |
| Verification | Test, review, or gate |
| Revisit trigger | When to re-open |

### From threat → ticket / security story (definition of done)

A mitigation ticket (or Agile security story) is done when:

1. Threat ID is linked  
2. Control type is clear (preventive / detective / corrective)  
3. Acceptance criteria are testable (prefer ASVS/MASVS-style wording when applicable)  
4. Owner and milestone exist  
5. Security requirements text can be pasted into the design doc / backlog  
6. Negative constraint is explicit when relevant (“As a user I must **not** …”)

**Cornucopia closure habit:** after play, review applicable threats → pull “what are we going to do about it?” guidance → create user stories, specs, and test cases in the tracker under the feature you modeled — not in a private security wiki.

Accepted threats use the same exception fields as vuln debt ([Vulnerability-Management/4](../Vulnerability-Management/4_Exception_And_Acceptance_Workflows.md)): scope, residual risk, compensation, owner, expiry.

### Threat modeling inside a security design review

| Activity | Relationship |
|----------|--------------|
| **Security design review** | Broader gate: architecture vs requirements, patterns, shared services |
| **Threat modeling** | Core technique *inside* that gate for new/high-risk designs |

Lightweight design review is enough for routine low-risk changes. Formal TM (diagram + method + tickets) when auth, billing, PII, partners, or novel architecture lands. Reusable **patterns** (API authorizer shape, secrets handling, egress controls) should emerge from repeated models so the next review is faster.

### Validation checklist (question 4 — expanded)

| Check | Pass criteria |
|-------|---------------|
| Diagram fidelity | Builders + tester agree it matches runtime intent |
| Method fit | Technique matches scope; gaps named |
| Response completeness | Every high/medium threat has a decision |
| Mitigation adequacy | Residual risk accepted explicitly if not fully mitigated |
| Detection / response | Serious threats have “how would we know?” and “who is paged?” — prevention alone is half a job |
| Documentation | Artifacts findable from the service’s design home |
| Testability | At least the top mitigations have failing tests if regresssed |
| Stakeholder review | Not only security signed off |
| Change control | Model versioned with the design; empty-whiteboard start beats a huge Visio nobody but the author understands |

NIST **SSDF PW.1.2** (SP 800-218): keep design-time risk decisions current as the software changes — same spirit as Manifesto continuous refinement.

### Revisit triggers (operationalize)

| Trigger | Minimum action |
|---------|----------------|
| New trust boundary | Delta DFD + STRIDE on new crossings |
| New personal / payment data | STRIDE + LINDDUN pass ([4](./4_Attack_Trees_And_LINDDUN.md)) |
| New IdP / partner / admin plane | Spoofing, elevation, disclosure review |
| Shared-responsibility / cloud topology change | Update edges; re-check IAM and stores |
| Incident / near-miss on modeled path | Patch model + controls; add test |
| Major dependency or authz model change | Re-open Q2–Q3 |

```text
Model (Dragon / TMT / doc)
  → threat records
    → tickets / exceptions
      → tests & delivery gates
        → production evidence
          → revisit on triggers → refine diagram
```

### Metrics that show the loop works (lightweight)

| Signal | Healthy |
|--------|---------|
| % high threats with owner + due date | Near 100% before launch |
| Age of accepted threats past expiry | Zero |
| Mitigations with automated/manual test link | Rising |
| Models updated after boundary-changing releases | Non-zero |

Vanity: number of models filed with no tickets.

### Challenges tooling cannot fix

| Problem | Non-tool fix |
|---------|--------------|
| Wrong diagram | Meeting 1 with tester ([1](./1_Workshop_Process.md)) |
| No security fluency | Co-facilitation + training |
| Compliance theater | Measure mitigations shipped |
| Cloud complexity | Shared-responsibility checklist in Q1 |

## Apply

1. Pick one tooling path for the next quarter (doc-only, Threat Dragon, or TMT) and write the team rule in one paragraph.  
2. Import or redraw one real service; run analysis; triage ten threats with status + notes.  
3. Open tickets for the top five mitigations using the schema above.  
4. Add one regression test (or explicit test plan step) for the highest mitigate.  
5. Publish revisit triggers in the service runbook.  
6. Run a Q4 review with an outsider; file diagram fixes before calling the model “signed off.”

### Self-check

- [ ] Contrast whiteboard vs Threat Dragon vs TMT vs pytm in one line each  
- [ ] Recite TMT threat statuses and when Not Applicable is honest  
- [ ] List mandatory threat-record fields  
- [ ] Explain SSDF PW.1.2 in threat-modeling language  
- [ ] Name five revisit triggers  
- [ ] Give one vanity metric to refuse  

## Next

- [AI and agentic threat modeling](./6_AI_And_Agentic_Threat_Modeling.md)
- Back to [Threat-Modeling README](./README.md) · secure SDLC in [AppSec](../AppSec/README.md) · first-principles network path in [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive).

## Further reading

- [OWASP Threat Dragon](https://owasp.org/www-project-threat-dragon/)
- [Microsoft Threat Modeling Tool overview](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool)
- [Microsoft Learn — Getting started](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-getting-started)
- [OWASP — Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [NIST SP 800-218 — SSDF](https://csrc.nist.gov/pubs/sp/800/218/final)
- [Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- [OWASP Cornucopia — how to play](https://cornucopia.owasp.org/how-to-play)
- [OWASP Security Culture — Threat Modelling](https://owasp.org/www-project-security-culture/v10/6-Threat_Modelling/)
- [OWASP pytm](https://owasp.org/www-project-pytm/)
- [Threagile](https://github.com/Threagile/threagile)
- AI/agent methods (depth in [6](./6_AI_And_Agentic_Threat_Modeling.md)): [CSA MAESTRO](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro) · [MITRE ATLAS](https://atlas.mitre.org/) · [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/)
