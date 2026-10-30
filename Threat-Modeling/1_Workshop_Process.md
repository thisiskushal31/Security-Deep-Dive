# Workshop process and facilitation

[← Threat-Modeling](./README.md)

> **Progression:** concept → advanced → apply.

How teams run threat modeling as engineering work — agendas, roles, artifacts, and sign-off — not a poster session. Diagram depth: [2](./2_DFD_And_STRIDE_In_Practice.md). Literacy seed: [Foundations/12](../Foundations/12_STRIDE_And_Threat_Modeling.md).

## Concept

**Threat modeling** is analyzing representations of a system to highlight concerns about security and privacy characteristics ([Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)). In application security it is a structured, repeatable process: model the system from a security perspective, identify applicable threats, decide responses, and keep the model alive with the system ([OWASP Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)).

Why bother (same sources):

| Benefit | What you gain |
|---------|----------------|
| Risks early | Design flaws cost less than production incidents |
| Security awareness | Team practices “think like an attacker” on *this* design |
| Visibility of the TOE | Forced clarity on data flows, trust boundaries, actors |

### Four questions (process spine)

| # | Question | Phase name (OWASP) | Concrete session output |
|---|----------|--------------------|-------------------------|
| 1 | What are we working on? | System modeling / decomposition | Scoped DFD (or equivalent) + assumptions + data classes |
| 2 | What can go wrong? | Threat identification & ranking | Threat list tied to diagram elements, ranked |
| 3 | What are we going to do about it? | Response & mitigations | Mitigate / eliminate / transfer / accept + owners |
| 4 | Did we do a good enough job? | Review & validation | Stakeholder review, testable mitigations, stored artifacts |

Microsoft SDL names five engineering steps that map onto the same loop: **define security requirements → create an application diagram → identify threats → mitigate → validate**. Requirements often start implicit in the problem statement (“move sales files weekly”) before anyone says “security.”

![Microsoft SDL threat modeling cycle](../Assets/Threat-Modeling/Microsoft_SDL_Threat_Modeling_Approach.png)

*Image: Diagram → Identify → Mitigate → Validate cycle — [Microsoft Threat Modeling Tool getting started](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-getting-started).*

```text
Security requirements (often latent in the business ask)
  → Diagram (DFD + trust boundaries)
    → Identify (STRIDE / other method)
      → Mitigate (or eliminate / transfer / accept)
        → Validate → refine diagram as design changes
```

### Depth selector (do not over-process every PR)

| Situation | Depth |
|-----------|--------|
| Day-to-day vuln / bug triage | Informal four questions in your head or a 15–30 min whiteboard — not a 10-page STRIDE doc |
| No new trust boundary; no new sensitive data class; small internal change | Short checklist on the design note / PR |
| New public API, personal data, payments, partner, or major architecture change | Facilitated workshop (60–120 min) + written mitigations |
| High business impact, fraud, multi-team, compliance narrative required | Multi-stage method (e.g. PASTA) — [3](./3_PASTA_Staged_Walkthrough.md) |

**Security is never a binary “secure.”** A useful model says: *under these assumptions, against this class of attacker, these controls hold.* Write assumptions and attacker capability explicitly (what they can and cannot do).

### First-class session deliverables (beyond the diagram)

| Deliverable | Why practitioners keep asking for it |
|-------------|--------------------------------------|
| **Attacker model** | Capabilites, access, and motives you actually face (insider, stolen token, abused workflow — not only nation-state zero-days) |
| **In-scope attacks** | What this model is trying to stop |
| **Out-of-scope attacks** | Explicit non-goals (e.g. physical seizure of the laptop) so debates end |
| **Threat statements** | Prefer `[actor] can [action] because [weakness], resulting in [impact]` — not a vague worry or a lone STRIDE letter |
| **Global vs component models** | Org/security may keep a thin global model; teams own sub-models for their services — both stay linked |

During **elicitation (Q2), do not jump into solutions.** Capture the threat first; design mitigations in Q3. Sidetracking into architecture debates mid-brainstorm kills coverage.

### Mature-team traps (after you already “do TM”)

| Trap | Fix |
|------|-----|
| Model of the *plan*, not production | Include hotfixes, runbook steps, “temporary” flags, fail-open paths |
| STRIDE mad-libs on every node | Skip forced categories on low-signal nodes; write concrete threats where risk is real |
| Flat threat lists only | Add at least one **chained** path (attack tree / multi-step) for the crown jewel |
| Happy-path only | Model degraded states: queue backup, circuit breaker, auth outage |
| Same three architects every time | Rotate in on-call / 3 AM owners |
| Threat register with no owner/deadline | Ticket or time-boxed accept — undecided is a decision to do nothing |
| Workshop artifact never revisited | Tie to design/CI change triggers ([5](./5_Tooling_And_Closing_The_Loop.md)) |

### Threat modeling vs baseline requirements

| Tool | Job |
|------|-----|
| **ASVS / Top 10 / secure coding checklists** | Baseline “table stakes” controls every app of this class should have |
| **Threat modeling** | Discover *this* design’s abuse paths, business-logic holes, and non-standard risks checklists miss |

Mature habit: apply baseline requirements *and* threat-model custom/high-risk design. Do not pretend a checklist replaces Q2 on novel workflows — and do not invent threats that are already covered by a required ASVS control without mapping to it ([5](./5_Tooling_And_Closing_The_Loop.md)).

## Advanced

### Manifesto values and principles (facilitation law)

**Values** (prefer left over right): culture of finding/fixing design issues over checkbox compliance; people and collaboration over processes/tools; journey of understanding over a security snapshot; doing threat modeling over talking about it; continuous refinement over a single delivery.

**Principles that make sessions work:**

| Principle | Practice in the room |
|-----------|----------------------|
| Improve security/privacy via early and frequent analysis | Book the session in design, not after code freeze |
| Align with development practices; scope manageable slices | One service / one epic per model revision |
| Outcomes valuable to stakeholders | Tickets and decisions owners care about — not a PDF score |
| Dialog establishes understanding; documents record it | Whiteboard first, then capture |

| Patterns | Anti-patterns |
|----------|----------------|
| Systematic approach | Hero threat modeler |
| Informed creativity (craft + science) | Admiration for the problem (analysis forever) |
| Varied viewpoints | Tendency to overfocus (only adversaries, only assets, only one technique) |
| Useful toolkit | Perfect representation (never ship a model) |
| Theory into practice | |

### Who must be in the room

| Role | Owns in the session |
|------|---------------------|
| Facilitator (security or trained eng) | Four-question rails, timebox, method choice |
| Feature / service owner | Mitigation priority; residual-risk acceptance |
| Engineers who will build it | Diagram truth; implementability |
| Tester / QA | Corner cases; validation ideas (Microsoft’s worked example: the tester often finds missing components) |
| Ops / SRE | Runtime DoS, secrets, deploy identity, backups |
| Privacy / GRC (personal data) | LINDDUN prompts — [4](./4_Attack_Trees_And_LINDDUN.md) |

Microsoft’s getting-started story is deliberately multi-role: developer + PM draw; tester challenges the diagram in a **second** meeting. Plan for that skepticism — it is a feature.

### Two-meeting pattern (high-stakes)

| Meeting | Goal | Timebox |
|---------|------|---------|
| 1 — Sync + diagram | Teach process briefly; walk DFD until the room agrees it matches reality | 45–60 min |
| 2 — Threats + sign-off | Walk ranked threats, agree responses, sign off, check into source control | 60–90 min |

Do not combine “argue about how the web server is built” and “sign off mitigations” in one exhausted hour if the diagram is still wrong.

### Sample agendas

**90-minute single session (common case)**

```text
0–10    Scope, out-of-scope, assumptions, security requirements (Q1 start)
10–30   Draw / correct DFD + trust boundaries + data classes
30–55   Threat elicitation (STRIDE-per-element or chosen method) — Q2
55–75   Responses, owners, tickets / exceptions — Q3
75–90   Review checklist, tests, revisit triggers — Q4
```

**Checklist-only (low depth)** — answer the four questions in the PR template; attach a four-box diagram; link tickets.

### Onboarding facilitation: card games (official)

When the team has never threat-modeled, **play a structured game after the diagram exists** — then graduate to STRIDE-per-element workshops.

| Game | Role |
|------|------|
| **Elevation of Privilege (EoP)** (Shostack / Microsoft SDL) | STRIDE-suited cards with concrete threat prompts; Privacy suit in newer decks; 3–6 players |
| **OWASP Cornucopia** | Agile-friendly; maps threats → security requirements / user stories; links toward ASVS, CAPEC, SAFECode stories |

**Actionable-threat test** (EoP / Cornucopia rule of thumb): if the room argues, ask *“Would we take an actionable bug, feature request, or design change for that?”* If yes, record it. Articulate threats so they are **clear, testable, and addressable**.

EoP play spine: draw the system first → deal → play a suit → announce how the card’s threat hits *this* design → record → next hand. Aces invent a new threat of that suit.

### Security requirements before the diagram

Customers rarely list security. Pull requirements from the problem statement:

| Business ask (example) | Latent security requirement |
|------------------------|-----------------------------|
| Collect sales files weekly | Protect data in transit and at rest; authenticate the sales force |
| Produce reports from a DB | Authorize readers; prevent spoofed report consumers |
| Partner API | Mutual auth; least privilege; audit who called |

Write 3–7 requirements on the board *before* STRIDE so mitigations have a target.

### Responses (question 3) — depth

| Response | Means | Document |
|----------|-------|----------|
| **Mitigate** | Reduce likelihood or impact with a control | How + verification test |
| **Eliminate** | Remove the feature / path | What shipped instead |
| **Transfer** | Contractual/financial shift | Remaining technical residual risk + who owns controls |
| **Accept** | Live with residual risk | Owner, expiry, compensation — same discipline as [Vulnerability-Management/4](../Vulnerability-Management/4_Exception_And_Acceptance_Workflows.md) |

Every in-scope threat gets a primary response. “Needs investigation” is a **status**, not a final response (Microsoft TMT statuses: Not Started / Needs Investigation / Mitigated / Not Applicable).

### Artifacts to keep (minimum set)

| Artifact | Location habit |
|----------|----------------|
| DFD / model file | Next to design doc or in Threat Dragon / TMT repo path |
| Threat table | Same doc or tool export |
| Tickets | Backlog linked from threat IDs |
| Assumptions list | Top of design doc |
| Sign-off note | Who, when, version of diagram |

### Challenges and fixes

| Challenge | Fix |
|-----------|-----|
| Devs lack security fluency | Security co-facilitates; short STRIDE drills |
| Schedule pressure | Scope to one boundary; checklist for the rest |
| Siloed teams | Require owner + builder + tester in meeting 1 |
| Model as compliance theater | Measure tickets closed and tests added, not models filed |

## Apply

1. Take one in-flight design. Write latent security requirements (5 bullets) from the business ask.  
2. Schedule the right depth (checklist vs 90 min vs two-meeting).  
3. Run Q1–Q4; leave with tickets that have owners and verification notes.  
4. Store the diagram where the service’s design already lives.  
5. Add revisit triggers: new trust boundary, new data class, new partner/IdP, incident on a modeled path.  
6. Book a 30-minute Q4 review with someone who was *not* in the first drawing session.

### Self-check

- [ ] Recite the four Manifesto questions and map them to SDL’s five steps  
- [ ] Explain why a tester’s skepticism improves the model  
- [ ] List mandatory participants for an API that stores personal data  
- [ ] Write four response types with one documentation rule each  
- [ ] Name three Manifesto anti-patterns and how you would kill them in a meeting  
- [ ] Write one in-scope and one out-of-scope attack for a system you know  
- [ ] Rewrite a vague threat into actor/action/weakness/impact form  

## Next

- [DFD and STRIDE in practice](./2_DFD_And_STRIDE_In_Practice.md)

## Further reading

- [Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)
- [OWASP — Threat Modeling Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html)
- [Microsoft — SDL Threat Modeling](https://www.microsoft.com/en-us/securityengineering/sdl/threatmodeling)
- [Microsoft Learn — Getting started with the Threat Modeling Tool](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-getting-started)
- [Shostack — Elevation of Privilege game](https://shostack.org/games/elevation-of-privilege)
- [USENIX — Elevation of Privilege paper](https://www.usenix.org/system/files/conference/3gse14/3gse14-shostack.pdf)
- [OWASP Cornucopia](https://cornucopia.owasp.org/)
