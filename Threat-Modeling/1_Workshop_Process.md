# Workshop process and facilitation

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

How to run a threat-modeling session that produces decisions — not a poster. Literacy baseline: [Foundations/12](../Foundations/12_STRIDE_And_Threat_Modeling.md). Diagram depth: [2](./2_DFD_And_STRIDE_In_Practice.md).

## Concept

**Threat modeling** is analyzing representations of a system to highlight concerns about security and privacy characteristics ([Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)).

Every useful session answers four questions:

| # | Question | Session output |
|---|----------|----------------|
| 1 | What are we working on? | Scoped diagram / DFD + assumptions |
| 2 | What can go wrong? | Ranked threat list |
| 3 | What are we going to do about it? | Mitigate / eliminate / transfer / accept — with owners |
| 4 | Did we do a good enough job? | Review checklist + tests / revisit triggers |

Microsoft’s SDL framing is the same idea as five engineering steps: define security requirements → diagram → identify threats → mitigate → validate.

| Depth | When |
|-------|------|
| Short checklist on PR / design note | No new trust boundary; no new sensitive data class |
| Facilitated workshop (60–120 min) | New API, partner, personal data, payments, major change |
| Multi-stage method (e.g. PASTA) | Business-impact-driven / high-stakes systems ([3](./3_PASTA_Staged_Walkthrough.md)) |

## Advanced

### Manifesto values (facilitation compass)

Value finding and fixing design issues over checkbox compliance; people and collaboration over tool worship; continuous refinement over a single delivery; **doing** threat modeling over talking about it.

| Pattern to use | Anti-pattern to kill |
|----------------|----------------------|
| Systematic approach | Hero threat modeler |
| Varied viewpoints (eng + security + product) | Admiration for the problem (analysis forever) |
| Useful toolkit | Perfect representation (never ship a model) |
| Theory into practice | Overfocus on adversaries *or* assets *or* one technique |

### Who is in the room

| Role | Why |
|------|-----|
| Facilitator (security or trained engineer) | Keeps four questions on rails; timeboxes |
| Feature / service owner | Owns mitigations and accepts residual risk |
| Engineer who will build it | Reality-checks diagram and fixes |
| Ops / SRE (when runtime matters) | Denial-of-service, secrets, deploy identity |
| Privacy / GRC (when personal data) | LINDDUN / compliance prompts ([4](./4_Attack_Trees_And_LINDDUN.md)) |

Invite security into the room when the team is early on the learning curve — joint sessions teach faster than a review of a finished diagram.

### Sample agenda (90 minutes)

```text
0–10   Scope + assumptions (question 1)
10–25  Draw / refine DFD + trust boundaries
25–55  Threat prompts (STRIDE or chosen method) — question 2
55–75  Responses + owners + tickets — question 3
75–90  Review checklist + tests + revisit triggers — question 4
```

Record artifacts where the team already works (ticket epic, design doc, Threat Dragon file) — not a private security folder nobody opens.

### Responses (question 3)

| Response | Meaning |
|----------|---------|
| **Mitigate** | Reduce likelihood or impact with a control |
| **Eliminate** | Remove the feature / path that creates the threat |
| **Transfer** | Contractual / financial shift — still document residual technical risk |
| **Accept** | Time-boxed, owned acceptance ([Vulnerability-Management/4](../Vulnerability-Management/4_Exception_And_Acceptance_Workflows.md)) |

Every threat gets exactly one primary response. “We’ll see” is not a response.

### Challenges (expect them)

Developers often lack security fluency; schedules squeeze the session; siloed teams produce incomplete models. Fix with training, shorter scoped iterations, and cross-functional attendance — not with a longer PDF.

## Apply

1. Pick one in-flight design and schedule a 90-minute session with owner + two builders.  
2. Write scope and three assumptions on the first slide / doc section.  
3. Run the four questions; leave with tickets that have owners and due dates.  
4. Store the diagram next to the design doc.  
5. Add a revisit trigger: “new trust boundary or new data class → re-open model” ([Foundations/9](../Foundations/9_Change_Management_And_Security.md)).

### Self-check

- [ ] Recite the four Manifesto questions  
- [ ] Name Manifesto values that beat checkbox compliance  
- [ ] List who must be in a workshop for an API that stores personal data  
- [ ] Contrast mitigate / eliminate / transfer / accept in one line each  

## Next

- [DFD and STRIDE in practice](./2_DFD_And_STRIDE_In_Practice.md)

## Further reading

- [Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)
- [OWASP — Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [Microsoft — SDL Threat Modeling](https://www.microsoft.com/en-us/securityengineering/sdl/threatmodeling)
- [OWASP — Threat Modeling](https://owasp.org/www-community/Threat_Modeling)
