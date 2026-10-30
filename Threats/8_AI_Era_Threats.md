# AI-era threats (literacy)

[← Threats](./README.md)

> **Progression:** concept → advanced → apply.

How generative AI and machine learning change the threat picture: **attacks that use AI**, **attacks that target AI systems**, and **failures in AI design/implementation**. Classical Domain 2 ([1](./1_Actors_And_Motivations.md)–[7](./7_Mitigations.md)) still applies — phishing, malware, supply chain, DoS — but AI **amplifies scale/quality** and adds **model-specific** and **agent-specific** attack classes. Read this after the classical path so you can see what is *new* versus what is the *same threat, louder*.

Technique catalogs: [MITRE ATLAS](https://atlas.mitre.org/) (AI) · [ATT&CK](https://attack.mitre.org/) (host/network) via [Foundations/18](../Foundations/18_Adversary_Models_Kill_Chain_And_ATTCK.md) · [GRC](../GRC/1_NIST_ISO_And_ATTCK.md). Threat-*modeling* practice for LLM/agents: [Threat-Modeling/6](../Threat-Modeling/6_AI_And_Agentic_Threat_Modeling.md). App/agent build depth: [AI-Security](../AI-Security/README.md) · [AppSec](../AppSec/README.md).

No jailbreak recipes, red-team payloads, or misuse enablement steps here — definitions and program framing only.

## Concept

Two complementary official framings:

| Source | Core idea |
|--------|-----------|
| **CISA** (GenAI in high-stakes settings) | GenAI often **amplifies existing risks** (cheaper scale, more persuasive lures) rather than inventing wholly new cyber *classes* |
| **NIST AI 600-1** (GAI Profile) | GAI also introduces risks that are **novel to or exacerbated by** generative AI — including confabulation, information integrity, value-chain opacity, and a **larger attack surface** (prompt injection, poisoning, model compromise) *plus* lowered barriers for offensive cyber activity |
| **ENISA Threat Landscape 2025** | AI is used **as a weapon**, **as a target**, and increasingly **as a lure** (fake AI sites/installers); consumer-grade misuse still dominates, but **stand-alone malicious AI tooling** and **AI supply-chain** hits are rising |
| **CCCS** | Same three pillars as program practice: protect against **adversarial use of AI**, protect **AI systems**, protect **users and business processes** — AI actions **add to** classic IT hygiene, they do not replace it |

DHS / CISA cross-sector analysis groups system-level AI risk into three categories:

| Category | Meaning |
|----------|---------|
| **Attacks using AI** | Adversaries use AI to automate, enhance, plan, or scale cyber or physical compromise (including social engineering) |
| **Attacks targeting AI systems** | Adversaries attack the AI itself — data, models, inputs, availability, or privacy of the model |
| **Failures in AI design and implementation** | Deficiencies in planning, structure, implementation, execution, or maintenance cause malfunction or unintended harm (often without a dedicated adversary) |

```text
               ┌─────────────────────────────┐
               │     AI-era threat picture   │
               └──────────────┬──────────────┘
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
   Use AI as a weapon   Attack the AI stack   AI fails by design
   (phishing, deepfake,  (poison, evade,       (brittleness,
    malware assist,       inject, extract,      autonomy,
    recon, AI-as-lure)    agent hijack)         overreliance)
```

**AI is still software.** CISA Secure by Design: patching, memory safety where relevant, vulnerability IDs, SBOMs/**AIBOMs** (models, datasets, dependencies), privacy defaults — *then* AI-specific assurance. Compromising the host or pipeline often achieves the same end as attacking the model. Joint CISA / UK NCSC guidance structures secure AI work as **secure design → development → deployment → operation/maintenance**.

| Term | Meaning (working literacy) |
|------|----------------------------|
| **Predictive AI (PredAI)** | Models that classify / score / predict (fraud, malware detection, face match, IDS) |
| **Generative AI (GenAI / GAI)** | Models that generate content (text, image, audio, code, multimodal) |
| **LLM application** | Product that wraps a large language model with prompts, tools, retrieval, memory, UI — model as **component** |
| **Agentic AI** | Model with tools, memory, planning, and ability to take consequential actions — model as **actor**; attack surface expands (CCCS / Five Eyes: adopt cautiously; low-risk tasks; no broad unrestricted access) |
| **Frontier AI** | Most capable / newest models; CCCS notes dual-use pressure (faster recon and attack orchestration *and* defensive analytics) — still needs ownership, logging, and human gates for high-impact decisions |
| **Shadow AI** | Unsanctioned use of public GenAI tools with corporate or personal data — expands surface and leaks prompts ([CCCS](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049)) |
| **ATLAS** | MITRE’s living knowledge base of adversary tactics/techniques involving AI — use like ATT&CK for AI-shaped campaigns |
| **Confabulation** | NIST term for confidently stated but erroneous content (often called “hallucination”) |

## Advanced

### A. Attacks using AI (amplification of Domain 2)

These sit on top of notes [1](./1_Actors_And_Motivations.md)–[5](./5_Attack_Classes.md). AI lowers cost and raises persuasiveness; **mitigation families** often stay familiar (MFA, verify out-of-band, EDR, segmentation, backups).

| Pattern | Definition / effect | Ties to |
|---------|---------------------|---------|
| **AI-enhanced phishing / spear phishing** | Generated text/pages that look more natural, personalized, and multilingual at scale | [2](./2_Vectors_And_Surfaces.md) |
| **Voice cloning / synthetic audio** | AI-generated speech mimicking a known person for vishing, helpdesk abuse, or fraud | [2](./2_Vectors_And_Surfaces.md) · [Foundations/16](../Foundations/16_Human_Security_Literacy.md) |
| **Deepfakes (video/image)** | Synthetic media used to impersonate, harass, delegitimize, support influence, or drive fraud (including deepfake video meetings) | [1](./1_Actors_And_Motivations.md) |
| **Synthetic personas / inauthentic accounts** | AI-assisted fake profiles and content farms for social engineering or influence | [2](./2_Vectors_And_Surfaces.md) |
| **AI-assisted malware / evasion authoring** | Models used to draft or iterate malware and evasion ideas — delivery still classic; ENISA notes criminal “malicious AI” tooling trends | [4](./4_Malware.md) |
| **AI-assisted recon / vuln triage** | Faster parsing of public data, code, and vuln intel (CCCS **frontier AI** concern: automated discovery pressure) | [3](./3_Vulnerabilities.md) |
| **AI as lure** | Fake “AI product” sites or installers that deliver malware/ransomware (ENISA) | [2](./2_Vectors_And_Surfaces.md) · [4](./4_Malware.md) |
| **Influence / synthetic media campaigns** | Deepfakes and AI text to undermine trust — CISA notes foreign actors amplify false narratives | [1](./1_Actors_And_Motivations.md) |
| **Buggy / insecure AI-suggested code** | Developers paste GenAI code that omits checks — accidental vulnerability introduction (CCCS) | [AppSec](../AppSec/README.md) |

DHS subcategory examples under “attacks using AI” also include AI-enabled cyber compromises, automated physical attacks, target identification, supply-chain disruption assists, IP theft/reverse-engineering assists, and weapon-development assists — treat CBRN/physical extremes as **national threat-picture awareness**, not lab content (also NIST AI 600-1 CBRN risk).

**Program takeaway (CCCS):** treat voice/video as **untrusted until verified**; out-of-band for money/access; phishing-resistant MFA; do not invent “AI magic” controls while skipping patching and backups.

### B. NIST AI 600-1 — GAI risks (unique or exacerbated)

Use this as the **program risk register lens** for generative AI (alongside the three DHS categories). Definitions paraphrased from NIST AI 600-1:

| # | Risk | One-line meaning |
|---|------|------------------|
| 1 | **CBRN information or capabilities** | Eased access to or synthesis of chemical/biological/radiological/nuclear-related nefarious information or design help |
| 2 | **Confabulation** | Confidently wrong outputs that mislead users or downstream systems |
| 3 | **Dangerous, violent, or hateful content** | Easier production/access to inciting, radicalizing, threatening, or illegal-activity guidance |
| 4 | **Data privacy** | Leakage, unauthorized use, disclosure, or de-anonymization of sensitive/PII data |
| 5 | **Environmental impacts** | High compute for train/operate and related ecosystem effects |
| 6 | **Harmful bias or homogenization** | Amplified bias, subgroup performance gaps, undesired output homogeneity |
| 7 | **Human-AI configuration** | Anthropomorphizing, automation bias, over-reliance, emotional entanglement, or aversion |
| 8 | **Information integrity** | Easier generation/spread of content that blurs fact/opinion/fiction — large-scale mis/disinformation |
| 9 | **Information security** | Lowered barriers for offensive cyber (phishing, malware, vuln discovery assist) **and** expanded attack surface on the GAI system itself |
| 10 | **Intellectual property** | Unauthorized replication of protected content; easier exposure of trade secrets / plagiarism patterns |
| 11 | **Obscene, degrading, and/or abusive content** | Eased production of harmful synthetic imagery (NIST names illegal/abusive categories including CSAM and NCII — policy/governance risk; not a lab topic here) |
| 12 | **Value chain and component integration** | Opaque upstream components, unvetted suppliers, unclean/improper data — weak accountability for downstream users |

NIST’s own grouping hint: **technical/model** risks · **misuse by humans** · **ecosystem/societal** risks — many items cross-cut.

### C. Attacks targeting AI systems (NIST AML + DHS)

NIST AI 100-2e2025 separates **Predictive AI** and **Generative AI** by attacker goals: availability, integrity, privacy, (GenAI) misuse enablement, and supply chain.

#### Lifecycle view

```text
Training / fine-tune / data pipeline     Deployment / inference / agents
───────────────────────────────          ───────────────────────────────
Data poisoning                           Evasion (adversarial examples)
Model poisoning / backdoors              Prompt injection (direct/indirect)
Supply-chain poisoned artifacts          Jailbreak / misuse enablement
Coding-assistant config / “rules”        Privacy: membership, extraction,
  backdoors (ENISA)                        reconstruction, prompt extraction
Poisoned hosted ML models / deps         Unbounded consumption / DoS-like
                                         Agent goal hijack / tool misuse
```

#### Predictive AI — core attack classes

| Class | Definition (NIST-aligned) |
|-------|---------------------------|
| **Data poisoning** | Adversary inserts or modifies training samples so the learned model misbehaves |
| **Model poisoning** | Adversary controls model parameters (federated learning / supply-chain model delivery) |
| **Backdoor poisoning** | Poisoning that triggers misbehavior when a **backdoor pattern** is present |
| **Targeted poisoning** | Poisoning aimed at specific samples or behaviors |
| **Evasion** | At deployment, craft **adversarial examples** that change predictions while looking near-normal |
| **Membership inference** | Infer whether a record was in the training set |
| **Reconstruction / data extraction** | Recover training data or sensitive features from model access |
| **Property inference** | Infer global properties of the training distribution |
| **Model extraction** | Steal or approximate model behavior via queries |
| **Energy-latency / availability** | Queries crafted to exhaust resources or degrade service |

#### Generative AI / LLM — core attack classes

| Class | Definition (NIST / OWASP-aligned) |
|-------|-----------------------------------|
| **Prompt injection (direct)** | User-path input overrides intended instructions / system prompt behavior |
| **Indirect prompt injection** | Hostile instructions ride in retrieved/tool content (docs, web, email, RAG, images, tool output) |
| **Jailbreak / misuse enablement** | Circumvent safety restrictions so the model produces disallowed outputs |
| **Prompt extraction** | Steal system prompts or hidden instructions |
| **Data / model poisoning (GenAI)** | Poison pre-training, fine-tuning, or published model artifacts |
| **Sensitive information disclosure** | Model leaks secrets, PII, proprietary data, or internal context |
| **Hidden context exposure** | Privileged context (system prompt, tools, memory, private retrieval) reaches attackers |
| **Excessive agency** | Too much autonomy/tool privilege — injection or error becomes real-world damage |
| **Improper output handling** | Downstream trusts model output without validation/encoding |
| **Vector / embedding weaknesses** | Weaknesses in RAG/embedding stores |
| **Unbounded consumption** | Uncontrolled cost, tokens, compute, or rate |
| **Misinformation (model output)** | Fluent wrong content that users or tools act on |

#### OWASP Top 10 for LLM Applications (2026) — model as component

| ID | Risk |
|----|------|
| LLM01 | Prompt Injection |
| LLM02 | Sensitive Information Disclosure |
| LLM03 | Excessive Agency |
| LLM04 | Supply Chain |
| LLM05 | Data and Model Poisoning |
| LLM06 | Unbounded Consumption |
| LLM07 | Misinformation |
| LLM08 | Hidden Context Exposure |
| LLM09 | Vector and Embedding Weaknesses |
| LLM10 | Improper Output Handling |

#### OWASP Top 10 for Agentic Applications (2026) — model as actor

When the system plans, calls tools, keeps memory, or multi-agent coordinates, pair LLM Top 10 with **ASI**:

| ID | Risk | One-line meaning |
|----|------|------------------|
| ASI01 | **Agent goal hijack** | Manipulated inputs redirect goals, planning, or multi-step behavior |
| ASI02 | **Tool misuse and exploitation** | Legitimate tools used unsafely (wrong action, chaining, over-invocation) |
| ASI03 | **Identity and privilege abuse** | Delegation, inherited credentials, confused-deputy / non-human identity gaps |
| ASI04 | **Agentic supply chain** | Malicious/compromised models, tools, MCP/A2A descriptors, registries, datasets |
| ASI05 | **Unexpected code execution** | Generated or tool-driven code runs unintended on host/container |
| ASI06 | **Memory and context poisoning** | Persistent memory/RAG/shared context corrupted across sessions |
| ASI07 | **Insecure inter-agent communication** | Spoofed, tampered, or unauthenticated agent-to-agent messages |
| ASI08 | **Cascading failures** | One fault fans out across agents/workflows |
| ASI09 | **Human-agent trust exploitation** | Automation bias / persuasive agent pushes humans to approve harm |
| ASI10 | **Rogue agents** | Agent drifts from authorized behavior; harmful autonomy after integrity loss |

CCCS / Five Eyes caution: prefer **low-risk, non-sensitive** agentic tasks; never grant broad unrestricted access; keep human oversight for high-impact decisions; align agent risk with the existing security model.

#### AI supply-chain specifics (ENISA + OWASP + CCCS)

| Pattern | Meaning |
|---------|---------|
| **Poisoned hosted ML models / datasets** | Malicious artifacts in public model/data hubs |
| **Trojanised AI dependencies** | Compromised packages (for example language-ecosystem indexes) in AI pipelines |
| **Rules / config file backdoor** | Malicious instructions planted in coding-assistant configuration that the assistant will follow |
| **MCP / tool-descriptor poisoning** | Hostile metadata or look-alike tools in agent tool ecosystems |
| **Slopsquatting** | Attackers register packages whose names match **hallucinated** dependency names suggested by coding assistants (ENISA-noted term) |
| **AIBOM / SBOM gaps** | Missing inventory of models, datasets, prompts, tools, and deps |

#### DHS “attacks on AI” subcategories (cross-check)

Adversarial manipulation of algorithms/data/sensors · evasion · interruption of service · loss of data · model inversion and extraction — map onto NIST AML rows above.

### D. Failures in AI design and implementation (non-adversarial / structural)

Still a **threat source** under NIST’s structural/accidental framing ([1](./1_Actors_And_Motivations.md)).

| Failure mode | Meaning |
|--------------|---------|
| **Autonomy** | Excessive permissions or poorly defined parameters → unexpected harmful actions |
| **Brittleness** | Breaks outside the original problem context |
| **Inscrutability** | Hard to interpret/diagnose — slows detection and correction |
| **Inadvertent systemic flaws** | Unintentional design defects that disrupt ops or create gaps |
| **Inconsistent maintenance** | Models/supporting systems not updated → drift, outages |
| **Interop / config with non-AI systems** | Integration bugs between AI and classic IT/OT |
| **Overreliance / underreliance** | Humans stop supervising — or skip useful AI defenses |
| **Statistical bias** | Distorted data/design → biased or erroneous decisions |
| **SME shortage** | Wrong selection, install, or interpretation of AI systems |
| **Supply-chain (design)** | Third-party AI/data not validated |
| **Shadow AI / weak acceptable use** | Staff paste secrets into public tools; no inventory of sanctioned models |

### E. Mitigations literacy (AI-specific + classic)

#### CCCS top AI security actions (three pillars — awareness)

| Pillar | Example actions |
|--------|-----------------|
| **1 — Against adversarial use of AI** | Prompt-injection/jailbreak *architectural* mitigations; deepfake/impersonation defenses (media authenticity, meeting PINs, out-of-band); harden against AI-scaled phishing/fraud (patch edge systems, bot detection, rate limits, Zero Trust) |
| **2 — Protect AI systems** | Authorized testing/red teaming; data-poisoning defenses (provenance, curated datasets); model-theft / extraction monitoring; secure AI engineering + AIBOM/SBOM + signed artifacts |
| **3 — Users and business processes** | Privacy/vendor/contract controls; shadow-AI governance; human-in-the-loop + kill switches; resilience against drift, confabulation, bias, overreliance |

#### Control families (combined)

| Control family | Why it matters |
|----------------|----------------|
| **Out-of-band verification; voice/video untrusted by default** | Deepfake / voice-clone fraud |
| **Phishing-resistant MFA + mail hygiene** | AI-written lures still enter classic channels |
| **Least privilege / least agency for tools** | Bounds blast radius after injection or goal hijack |
| **Human approval + kill switch for consequential actions** | Money, identity, destructive tools, goal changes |
| **Treat retrieved content and tool outputs as untrusted** | Indirect prompt injection / RAG / MCP |
| **Validate / encode model outputs; sandbox code execution** | Improper output handling and unexpected execution |
| **Dataset/model validation; signed artifacts; AIBOM/SBOM** | Poisoning and AI supply chain |
| **Access control on training data, models, prompts, vector stores, memory** | Privacy and extraction — treat model access like training-data access |
| **Rate limits / cost / tool budgets** | Unbounded consumption and tool loops |
| **Alternate / manual process redundancy** | Brittleness and AI outage |
| **Secure AI lifecycle (design→ops)** | CISA/NCSC four-stage secure AI development |
| **Shadow AI policy + allow/deny lists** | Prompt leakage and unsanctioned models |
| **Logging / monitoring of agent goals, tools, and inter-agent traffic** | Goal hijack, cascades, rogue behavior |
| **Authorized AI red teaming** | Controlled collaborative testing — not unauthorized attack |
| **Watermarking / content authentication (where available)** | Assist synthetic-media identification — not a silver bullet |

Implementation homes: [7](./7_Mitigations.md) · [AI-Security](../AI-Security/README.md) · [AppSec](../AppSec/README.md) · [Defensive-Ops](../Defensive-Ops/README.md) · [Cloud-Security/3](../Cloud-Security/3_Supply_Chain.md).

### F. How this maps onto the rest of Threats

| Already covered | AI-era delta |
|-----------------|--------------|
| Actors / motivations | Same actors; AI raises **capability** and **obfuscation**; criminal “AI services”; AI-themed lures |
| Vectors | Higher-quality content on old channels; model APIs; RAG; agents; MCP/tools; coding-assistant configs; fake AI download sites |
| Vulnerabilities | Classic CVE/config on AI hosts (ENISA: AI software and supporting infra still have CVEs) + AML/LLM/ASI classes |
| Malware | AI-assisted authoring; malware disguised as AI tools |
| Attack classes | AML / LLM / ASI; deepfake-assisted fraud |
| Indicators | Synthetic media; anomalous model/agent behavior; poisoned retrieval; odd tool chains — still need correlation ([6](./6_Indicators_And_Analysis.md)) |
| Mitigations | Classic + agent privilege + output validation + AIBOM/supply chain + shadow-AI governance |

### Real cases (these already happened)

Short examples from official public reports so you know this is not theory. Links have the full story — this is **not** a how-to.

| What happened | Why it matters here | Where to read more |
|-----------------------|------------------------------|--------------------|
| **Deepfake / synthetic-media fraud** | Same BEC/vishing *goal* (money, secrets) with forged voice/video — treat media as untrusted until verified | [CCCS — Top 10 AI security actions](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049) (Action 2) |
| **Prompt injection against enterprise assistants** | Attacks **targeting AI** — untrusted content steers a privileged model/agent | NIST AI 100-2 / OWASP LLM01 · CCCS Action 1 cites production assistant injection patterns |
| **AI as lure (fake AI sites/installers)** | Classic malware delivery wearing a GenAI brand | [ENISA Threat Landscape 2025](https://www.enisa.europa.eu/sites/default/files/2025-11/ENISA%20Threat%20Landscape%202025.pdf) |
| **AI supply-chain / poisoned models & deps** | Value-chain risk: hosted models, packages, coding-assistant configs | ENISA 2025 · OWASP LLM04 / ASI04 · [CCCS Action 7](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049) |
| **GenAI amplifies phishing & influence** | “New” often means **scale and quality**, not a brand-new vector name | [CISA GenAI election risk fact sheet](https://www.cisa.gov/sites/default/files/2024-05/Consolidated_Risk_in_Focus_Gen_AI_ElectionsV2_508c.pdf) · ENISA 2025 |

## Apply

1. Classify into **using AI / targeting AI / design failure** (can be more than one).  
2. If **using AI**, name the classical Domain 2 path it amplifies and keep those mitigations.  
3. If **targeting AI**, name PredAI vs GenAI vs **agentic**, and the NIST-style goal (availability / integrity / privacy / misuse / supply chain).  
4. For an LLM app, walk **OWASP LLM Top 10**; if tools/memory/actions or multi-agent, also walk **ASI01–ASI10** — workshop method in [Threat-Modeling/6](../Threat-Modeling/6_AI_And_Agentic_Threat_Modeling.md).  
5. Map residual risks onto **NIST AI 600-1** where governance/board language is needed.  
6. Deployment bar: inventory models/data/tools (AIBOM), access control, output validation, rate/cost limits, human gates, kill switch, non-AI fallback for critical processes, shadow-AI policy.  
7. Use **ATT&CK** for host/network TTPs and **ATLAS** when the campaign centers on AI abuse or AI-system compromise.  
8. Never paste secrets into public GenAI tools; prefer private/approved instances.

### Self-check

- [ ] Recite DHS/CISA’s three AI risk categories and CCCS’s three pillars  
- [ ] Explain “amplifies existing risks” vs “new AI attack surface” vs NIST “novel or exacerbated”  
- [ ] Name at least eight NIST AI 600-1 GAI risks  
- [ ] Define data poisoning, evasion, membership inference, model extraction  
- [ ] Contrast direct vs indirect prompt injection  
- [ ] Name all ten OWASP LLM 2026 risks  
- [ ] Name all ten OWASP ASI 2026 risks at awareness level  
- [ ] Define shadow AI, AIBOM, slopsquatting, AI-as-lure  
- [ ] List three mitigations that matter when an agent has tools  
- [ ] State when to open ATLAS vs ATT&CK  
- [ ] Give one real case for attacks *using* AI and one for attacks *on* AI  

## Next

- [Mitigations](./7_Mitigations.md) (if you skipped) · [Threat-Modeling/6](../Threat-Modeling/6_AI_And_Agentic_Threat_Modeling.md) · [AI-Security](../AI-Security/README.md) · [AppSec](../AppSec/README.md) · [Defensive-Ops](../Defensive-Ops/README.md)

## Further reading

- [DHS — Safety and Security Guidelines for Critical Infrastructure Owners and Operators (AI)](https://www.dhs.gov/sites/default/files/2024-04/24_0426_dhs_ai-ci-safety-security-guidelines-508c.pdf)
- [CISA — Risk in Focus: Generative AI and the 2024 Election Cycle](https://www.cisa.gov/sites/default/files/2024-05/Consolidated_Risk_in_Focus_Gen_AI_ElectionsV2_508c.pdf)
- [CISA — Software Must Be Secure by Design, and Artificial Intelligence Is No Exception](https://www.cisa.gov/news-events/news/software-must-be-secure-design-and-artificial-intelligence-no-exception)
- [CISA / UK NCSC — Guidelines for Secure AI System Development](https://www.ncsc.gov.uk/files/Guidelines-for-secure-AI-system-development.pdf)
- [CISA — JCDC AI Cybersecurity Collaboration Playbook](https://www.cisa.gov/sites/default/files/2025-01/JCDC%20AI%20Playbook.pdf)
- [NIST AI 100-2e2025 — Adversarial Machine Learning](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)
- [NIST AI 600-1 — Generative AI Profile (AI RMF)](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)
- [NIST AI RMF (AI 100-1)](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST SP 800-218A — SSDF Community Profile for Generative AI](https://csrc.nist.gov/pubs/sp/800/218/a/final)
- [OWASP Top 10 for LLM Applications 2026](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/)
- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [OWASP GenAI LLM Top 10 (GitHub)](https://github.com/GenAI-Security-Project/GenAI-LLM-Top10)
- [MITRE ATLAS](https://atlas.mitre.org/) · [ATLAS data (GitHub)](https://github.com/mitre-atlas/atlas-data)
- [CCCS — Generative AI (ITSAP.00.041)](https://www.cyber.gc.ca/en/guidance/generative-artificial-intelligence-ai-itsap00041)
- [CCCS — Top 10 AI security actions (ITSAP.10.049)](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049)
- [CCCS — Frontier AI (ITSAP.10.050)](https://www.cyber.gc.ca/en/guidance/frontier-artificial-intelligence-itsap10050)
- [CCCS / Five Eyes — Careful adoption of agentic AI](https://www.cyber.gc.ca/en/guidance/careful-adoption-agentic-ai)
- [ENISA Threat Landscape 2025](https://www.enisa.europa.eu/sites/default/files/2025-11/ENISA%20Threat%20Landscape%202025.pdf)
- [ENISA — ETL 2025 news summary](https://enisa.europa.eu/news/etl-2025-eu-consistently-targeted-by-diverse-yet-convergent-threat-groups)
