# Analytic tradecraft and PIRs

[← Threat-Intelligence](./README.md)

> **Progression:** concept → advanced → apply.

How **CTI** teams decide *what to answer*, how they reason under uncertainty, and how they keep observation separate from judgment. Lifecycle vocabulary: [Foundations/19](../Foundations/19_Threat_Intelligence_Literacy.md). Intrusion models: [2](./2_Intrusion_Analysis_Models.md). Products: [3](./3_Finished_Intelligence_Products.md).

## Concept

### What CTI means

**CTI** = **Cyber Threat Intelligence**.

| Piece | Meaning |
|-------|---------|
| **Cyber** | About networks, systems, identities, malware, phishing, cloud, apps — digital attack surface |
| **Threat** | Someone or something that can harm confidentiality, integrity, or availability (actors, campaigns, techniques) |
| **Intelligence** | Not a raw dump of “bad” IPs — analyzed, relevant, timely product that helps someone **decide** (block, hunt, patch, brief, invest) |

In one line: CTI turns adversary-related data into **answers people can act on**.

| This is data / information | This is closer to intelligence |
|----------------------------|--------------------------------|
| A feed of 50,000 hashes | “These three loaders showed up in invoice phishing aimed at finance; hunt and mail rules for this week” |
| A vendor blog pasted into Slack | A short product with judgments, confidence, ATT&CK, and recommended next steps ([3](./3_Finished_Intelligence_Products.md)) |

**Who uses CTI?** Detection engineers, IR/SOC, vulnerability and GRC owners, leadership — anyone who needs “who/what/so what/what next” about adversaries. This folder is the **practice depth** (tradecraft, models, products, sharing, handoff). The vocabulary first pass is [Foundations/19](../Foundations/19_Threat_Intelligence_Literacy.md).

CTI without requirements is a feed subscription. **Priority intelligence requirements (PIRs)** are the short list of questions leadership and operators need answered so collection and analysis have a purpose.

| Question type | Example PIR | Decision it supports |
|---------------|-------------|----------------------|
| Who | Which intrusion sets regularly target our sector? | Where to spend detection / IR readiness |
| What | Which malware families or tools show up in our telemetry? | Signature / YARA / block priorities ([5](./5_Detection_Oriented_Handoff.md)) |
| So what | Which campaigns threaten payments, IP, or safety systems? | Board risk / insurance / investment |
| What next | What TTPs should purple exercises cover this quarter? | Authorized emulation scope |

**PIR quality bar** (write these into the ticket, not only the question):

| Field | Why |
|-------|-----|
| Owner | Who needs the answer |
| Decision / use | What changes if we answer it |
| Deadline / freshness | When the answer expires |
| Priority vs other PIRs | Force trade-offs |
| Likely sources | Internal telemetry, partners, open reporting, vendors |
| Success criteria | What “answered enough” looks like |

Related asks: **RFIs** (requests for information) are one-off questions; PIRs are standing or campaign-priority questions that drive the program. Both need owners.

NIST SP 800-150 frames sharing the same way: establish **goals and objectives** that support business and security policy, identify **internal sources first**, then scope what you will share and consume. Collection that does not map to a goal is noise with a logo.

```text
Decision needed
  → PIR (answerable question + owner + deadline + success criteria)
    → Collection plan (sources that can answer it)
      → Analysis (hypotheses + evidence + SATs)
        → Product (audience-shaped) ([3](./3_Finished_Intelligence_Products.md))
          → Feedback (did the decision improve?) → revise PIRs
```

### Five types of cyber threat information (NIST SP 800-150)

| Type | Meaning |
|------|---------|
| **Indicators** | Technical artifacts/observables suggesting attack imminent, underway, or past compromise (IP, domain, URL, hash, phishing subject, …) |
| **TTPs** | Actor behavior — tactics, techniques, procedures (order of ops, tools, delivery, exploits) |
| **Security alerts** | Brief human-readable notices (vulns, exploits, advisories) |
| **Threat intelligence reports** | Analyzed/enriched prose that supports decisions |
| **Tool configurations** | How to set tools that collect, exchange, or use threat information |

Programs that only chase indicators starve TTP and report depth — and stay at the bottom of the Pyramid of Pain ([5](./5_Detection_Oriented_Handoff.md)).

### Observation vs inference

| Habit | Practice |
|-------|----------|
| Separate fact from judgment | “Hash X on host Y at time Z” ≠ “APT group G did this” |
| Label source type | Internal telemetry · partner share · vendor report · open reporting |
| Corroborate before irreversible action | One unverified indicator → hunt/watch; high confidence + context → block |
| Record confidence | Low / moderate / high with *why* (corroboration, freshness, source track record) |
| Preserve provenance | Who provided it, how collected/transformed — NIST 800-150 tracking habit |

### Anonymity networks, privacy, and the “dark web”

Popular talk often treats “the dark web” as a crime mall. Start from the **privacy design**: networks like **Tor** exist so people can use the internet with stronger anonymity and censorship resistance. The same channel can be abused — that does not redefine the purpose of the tool.

**Law and reality (read this first):** Tor reduces how easily *network observers* link you to destinations. It is **not** a license for crime and **not** invisibility to investigations. Illegal activity remains illegal; law enforcement and platforms still investigate fraud, trafficking, intrusion, and similar offenses when evidence and jurisdiction allow. Privacy literacy ≠ “you won’t get caught.” Your org’s policy may also restrict work use of Tor — follow that.

#### Vocabulary (keep these separate)

| Term | Meaning |
|------|---------|
| **Clearnet / surface web** | Ordinary web indexed by search engines; normal browser |
| **Deep web** | Not indexed (paywalls, mail, bank portals, private DBs) — most of the useful internet; **not** “dark” and **not** crime |
| **Anonymity network** (Tor is the main public example) | Volunteer overlay: traffic through several encrypted relays so no single hop easily sees both who you are and where you go ([Tor overview](https://support.torproject.org/about-tor/how-tor-works/overview/)) |
| **Onion services** | Sites/services that live *on* Tor (`.onion`); clients reach them via Tor Browser |
| **“Dark web” (media shorthand)** | Usually “onion (and similar) services.” A **channel**, not a motive |

#### Why Tor exists (privacy-first)

Per the [Tor Project](https://support.torproject.org/about-tor/how-tor-works/overview/): resist traffic analysis and tracking; circumvent censorship; support journalists and sources, NGOs, people in sensitive medical or abuse-support communities, corporations doing sensitive research, and ordinary users who do not want every site correlating their identity. A diverse user base strengthens anonymity — privacy tools are not “only for bad people.”

Newsrooms also publish **onion mirrors** and use onion-based dropboxes so sources can reach them with less metadata exposure ([onion services talk](https://community.torproject.org/onion-services/talk/)).

#### How people actually access it (official path)

You do **not** need a sketchy “dark web browser” from a random video. The mainstream, documented path is **Tor Browser** from the Tor Project.

| Step | What to do | Why it matters |
|------|------------|----------------|
| 1. Get the real installer | Download only from [torproject.org/download](https://www.torproject.org/download/) or [download.torproject.org](https://download.torproject.org/) (Windows / macOS / Linux / Android). If the site is blocked, Tor documents **official mirrors** and **GetTor** (`gettor@torproject.org`) — not random third-party “Tor” APKs | Fake Tor bundles are a common malware vector |
| 2. Verify when you can | Tor recommends verifying the download **signature** ([downloading guide](https://support.torproject.org/tor-browser/getting-started/downloading/)) | Confirms the bits match what Tor published |
| 3. Install / launch | Follow Tor’s OS instructions ([installing](https://support.torproject.org/tor-browser/getting-started/installing/)) — e.g. run the Windows wizard, open the macOS app, or on Linux extract and run `start-tor-browser.desktop` | Tor Browser is a hardened Firefox fork wired to the Tor network |
| 4. Connect | First launch: Connect (or configure bridges if Tor is blocked on your network — Tor documents this) | Your ISP typically sees “you are using Tor,” not every site you open |
| 5. Use clearnet *through* Tor | Visit normal `https://` sites in Tor Browser | This is still “using Tor”; many privacy users never touch `.onion` |
| 6. Use onion services | Paste a full `.onion` address (modern v3 addresses are long: **56** characters + `.onion`) into Tor Browser ([onion services](https://support.torproject.org/tor-browser/features/onion-services/)) | You must **know or be given** the address — there is no single official “Google of the dark web” |

```text
Official Tor Browser
  → circuit of relays (privacy vs traffic analysis)
    → either: normal websites over Tor
    → or: .onion services (only reachable via Tor)
```

**Related tools (awareness, still official ecosystem):** Tor documents **Tor VPN (beta)** and points to **Tails** (amnesic live OS) for stronger operational privacy needs — advanced, not required to understand “what the dark web is.”

#### What Tor does *not* magically do

| Myth | Reality |
|------|---------|
| “Tor = invisible to law enforcement” | Tor hides a lot of *network* linkage; investigations still use endpoints, money trails, OPSEC mistakes, malware, warrants, and victim reports |
| “Everything on Tor is illegal” | False — large lawful privacy and journalism use |
| “Any Tor exit IP in my logs is an attacker” | False — could be a privacy user, researcher, or attacker; need context (CTI observation ≠ guilt) |
| “Random YouTube ‘dark web links pack’ is fine” | High phishing / malware risk; prefer known publishers (e.g. a newsroom’s published onion URL) |
| “Install extra VPN + random plugins for more stealth” | Tor’s own desktop guidance: be careful with stacking VPNs and **don’t** bolt on random browser add-ons that break fingerprint resistance |

#### Dual use (same channel, different intent)

| Lawful / privacy | Abuse (exists; do not equate with the channel) |
|------------------|-----------------------------------------------|
| Circumvent censorship; protect sources; reduce tracking | Some threat actors route C2 or scan traffic via Tor ([CISA AA20-183a](https://www.cisa.gov/news-events/cybersecurity-advisories/aa20-183a)) |
| Read a newspaper’s onion mirror | Fraud / intrusion staging behind anonymity |

**CTI habits:** treat Tor/onion indicators as **observations**; corroborate before block-or-attribute; org policy governs analyst collection; strip irrelevant PII when sharing (NIST 800-150). This note teaches **channel literacy and official access** — not underground markets, stolen-data shopping, or criminal persona playbooks.

```text
Privacy purpose (Tor / onion design)
  ≠  Criminal purpose (some actors abuse the channel)
Know how access works → judge evidence → follow law and policy
```

## Advanced

### Intelligence cycle (how real CTI work is organized)

Foundations/19 introduces the cycle. In practice, CTI jobs run it continuously — not as a one-off essay:

```text
Direction (PIRs / RFIs / stakeholders)
  → Collection (against a plan)
    → Processing (normalize, enrich, rate sources)
      → Analysis (SATs, models, judgments)
        → Dissemination (right product, TLP, audience)
          → Feedback → refine PIRs / collection
```

| Phase | Real-world habit |
|-------|------------------|
| **Direction** | Who decides what? Standing PIRs + ad-hoc **RFIs**; reject work with no consumer |
| **Collection** | Mix sources below; write a short **collection plan** (what, where, by when, legal/policy limits) |
| **Processing** | Dedupe feeds, extract claims/dates, enrich (WHOIS/pDNS/sandbox), preserve provenance |
| **Analysis** | Fuse sources; ACH/SATs; ATT&CK + Kill Chain/Diamond ([2](./2_Intrusion_Analysis_Models.md)) |
| **Dissemination** | Strategic brief ≠ STIX dump ([3](./3_Finished_Intelligence_Products.md), [4](./4_STIX_And_TAXII_Practice.md)) |
| **Feedback** | Did hunts/detections/decisions change? Retire or promote intel ([5](./5_Detection_Oriented_Handoff.md)) |

### Collection sources CTI teams actually use

Industry practice (e.g. CREST CTI guidance) fuses **multiple** source classes — not one vendor feed:

| Source class | Examples | Typical use |
|--------------|----------|-------------|
| **Internal** | EDR/SIEM summaries, phishing queue, IR tickets, sandbox detonations, vuln scan context | Ground truth for *your* environment |
| **OSINT / open reporting** | Vendor blogs, researcher write-ups, government advisories, court docs, news | Campaign context; always re-verify |
| **Community sharing** | ISACs/ISAOs, trusted peer lists, MISP communities, AIS-class feeds | Sector-relevant indicators + narratives |
| **Commercial CTI** | TIP subscriptions, finished reports, specialized underground-monitoring *services* (policy-bound) | Scale collection you cannot staff alone |
| **Malware / phishing as collection** | Samples → triage/sandbox → IOCs + behaviors | Infrastructure and TTP extraction ([5](./5_Detection_Oriented_Handoff.md)) |
| **Vuln / exploit context** | CVE + in-the-wild exploitation signals | Prioritize with [VM](../Vulnerability-Management/README.md) |
| **Geopolitical / business context** | Sector targeting trends, crisis-driven disruption risk | Strategic PIRs — not a substitute for technical evidence |

**Source evaluation (every claim):** Who said it? Do they have access? Motive to spin? Freshness? Corroborated by independent source? ICD 203 demands describing **quality and credibility** of sources — treat “vendor said APT-X” as a *claim*, not a fact, until you score it.

**RFI workflow (shop floor):** intake → map to PIR or reject → assign analyst → collect/analyze → product → consumer acknowledgment → close or escalate. Unowned RFIs become eternal tickets.

### Analytic standards (ICD 203 spine)

ODNI **ICD 203** sets Intelligence Community Analytic Standards that commercial CTI teams can borrow as product quality criteria. Analysis should be:

| Standard | CTI reading |
|----------|-------------|
| **Objective** | Evidence-led; challenge favorite narratives |
| **Independent of political considerations** | Do not shade judgments for convenience |
| **Timely** | Late perfect intel loses to early good-enough |
| **Based on all available sources** | Not only the loudest vendor blog |
| **Sound tradecraft** | Nine habits below |

**Nine analytic tradecraft standards** (ICD 203 / ODNI) — product checklist:

1. Describe quality and credibility of sources, data, and methods  
2. Express and explain **uncertainties** on major judgments  
3. Distinguish **information** from **assumptions and judgments**  
4. Incorporate **analysis of alternatives**  
5. Show customer relevance and implications  
6. Use clear, logical argumentation  
7. Explain change or consistency vs prior judgments; surface disagreements  
8. Make accurate judgments and assessments  
9. Use effective visuals where they help  

### Structured Analytic Techniques (SATs)

CIA *A Tradecraft Primer* (2009) groups techniques that challenge mind-sets. CTI teams do not need all of them every week — pick by problem type:

| Family | Techniques | When |
|--------|------------|------|
| **Diagnostic** | Key Assumptions Check · Quality of Information Check · Indicators/Signposts · **ACH** | Starting a project; controversial judgments; audit trail needed |
| **Contrarian** | Devil’s Advocacy · Team A/Team B · High-Impact/Low-Probability · What If? | Strong consensus you cannot afford to get wrong |
| **Imaginative** | Brainstorming · Outside-In · Red Team Analysis · Alternative Futures | Fresh angles; adversary mind-set; scenario work |

**Key Assumptions Check (start here often):** list the unspoken premises (“single actor,” “military training,” “white van” — Primer’s sniper example); rate each as solid / weak / unsupported; kill or watch the weak ones before they harden into “facts.”

**Devil’s Advocacy:** assign someone to build the *best case against* the prevailing line — labeled explicitly as advocacy so readers are not confused about the official view.

**What If?:** accept that an “unlikely” high-impact outcome matters, then explain *how it could happen* and what indicators would show it emerging — useful hedges for leadership products ([3](./3_Finished_Intelligence_Products.md)).

### Analysis of Competing Hypotheses (ACH) — full eight steps

Richards J. Heuer’s ACH (*Psychology of Intelligence Analysis*) forces alternatives to compete. Prefer **disproof** over hunting only confirming hits.

| Step | Action |
|------|--------|
| 1 | Identify possible hypotheses — brainstorm with diverse perspectives (include benign / misattribution / deception) |
| 2 | List significant evidence and arguments for and against |
| 3 | Matrix: hypotheses across top, evidence down the side; rate **diagnosticity** (does this item actually discriminate?) |
| 4 | Refine — drop non-diagnostic evidence; reconsider hypotheses |
| 5 | Tentative relative likelihoods — try to **disprove**; most probable often has *least against it*, not most for it |
| 6 | Sensitivity — what if a critical item were wrong, deceptive, or reinterpreted? |
| 7 | Report **relative** likelihood of *all* hypotheses, not only the winner |
| 8 | Milestones — future observations that would change the analysis |

ACH will not always pick truth; it reduces the chance you never considered a better hypothesis. Ideal for contested attribution and “same malware ⇒ same actor?” debates.

**Deception caution (Heuer):** absence of evidence of deception is not evidence of absence — well-run deception leaves little obvious trail. Do not reject a deception hypothesis only because you “don’t see it yet.”

### Cognitive traps

| Trap | Effect on CTI | Counter |
|------|---------------|---------|
| Mind-set / anchoring | Force-fit IOCs into the first campaign name | Key Assumptions Check; ACH |
| Confirmation bias | Remember only hits that match the vendor narrative | Seek disconfirming evidence |
| Availability | Loud media incidents overrule quieter relevant TTPs | PIR-driven collection |
| Overconfidence | High confidence without corroboration | Explicit uncertainty language |
| Satisficing | First “good enough” story wins | Full ACH or Devil’s Advocacy |

Mitigation is process, not willpower: SATs, peer review, written assumptions, product evaluation against ICD 203.

### Attribution caution

Naming an actor is a high-impact judgment. Prefer:

| Safer product language | Riskier leap |
|------------------------|--------------|
| “Activity consistent with reported TTPs of …” | “This *is* group X” from one hash |
| “Infrastructure previously used in …” | Equating shared tooling with shared operators |
| Cluster / activity group with confidence | Celebrity APT branding for every incident |

Describe *what happened* with Diamond/ATT&CK ([2](./2_Intrusion_Analysis_Models.md)) before arguing *who*.

### Requirements and sharing hygiene (NIST SP 800-150)

| Habit | Why |
|-------|-----|
| Inventory internal sources first | Sensors, tickets, malware sandbox, IR notes already produce CTI |
| Scope sharing | What may leave the org, under which markings, to whom |
| Sharing rules before volume | Trust, sensitivity, impact of disclosure (legal/privacy input) |
| Plan relationships *before* the incident | Do not invent trust during the breach |
| Protect PII in shares | Prefer indicators free of PII; redact irrelevant personal data |
| Metadata on indicators | Intended use, interpretation, relation to other indicators; update/retract paths |
| Feedback loop | PIRs die or mutate when consumers say the product did not help |

**Sharing challenges NIST calls out (awareness):** establishing trust; safeguarding sensitive data; interoperability/automation; classified-info friction; capacity to consume/publish without drowning; quality evaluation of external feeds.

### Worked PIR slice (illustrative)

| Field | Example |
|-------|---------|
| PIR | Are invoice-themed phishing kits delivering loaders that reach our finance VDI? |
| Owner | Detection eng + IR lead |
| Decision | Priority for mail gateway rules + endpoint hunts this sprint |
| Sources | Mail gateway logs, EDR, sandbox, ISAC share, open reports |
| Analysis | ACH: (A) targeted finance campaign (B) commodity kit spray (C) lab false positive |
| Product | Tactical card + STIX indicators ([3](./3_Finished_Intelligence_Products.md), [4](./4_STIX_And_TAXII_Practice.md)) |
| Feedback | Did hunts fire? FP rate? Retire or promote TTP |

## Apply

1. Write three PIRs (strategic, operational, tactical/technical) with owner, decision, deadline, and success criteria.  
2. Take one public incident narrative; list five *observations* and three *inferences*; mark confidence on each inference.  
3. Run full ACH (eight steps) on “why this phishing kit appeared,” including a non-malicious alternative; keep the matrix.  
4. Run a Key Assumptions Check on one strongly held team belief about your top adversary.  
5. Score one CTI product you consumed last month against the nine ICD 203 tradecraft habits (pass / weak / missing).  
6. Draft one attribution sentence that stays honest without overclaiming.  
7. From Tor’s official download page only: note the OS builds offered and one reason verifying signatures matters.  
8. Explain in writing: clearnet via Tor vs `.onion` access; one myth Tor does *not* solve; one CTI habit when a Tor exit appears in logs.

### Self-check

- [ ] Define CTI in one sentence (decision-supporting adversary insight, not a raw IOC dump)  
- [ ] Recite the intelligence cycle phases and one habit per phase  
- [ ] Name four collection source classes CTI teams fuse  
- [ ] Define PIR vs RFI and give one PIR tied to a decision  
- [ ] Name NIST’s five threat-information types  
- [ ] Separate observation, assumption, and judgment in one sentence each  
- [ ] Distinguish clearnet, deep web, anonymity network, onion service, and “dark web” (media term)  
- [ ] State the official way to get Tor Browser (and why random “dark web browsers” are risky)  
- [ ] Describe how to open an onion service (full `.onion` URL in Tor Browser)  
- [ ] Name one thing Tor does not guarantee (e.g. immunity from investigation of illegal acts)  
- [ ] Explain privacy purpose vs dual-use abuse without equating Tor with malice  
- [ ] Recite the five ICD 203 analytic standards  
- [ ] Outline ACH’s eight steps and what “diagnosticity” means  
- [ ] Name one diagnostic, one contrarian, and one imaginative SAT  
- [ ] Explain why “same malware family” ≠ “same actor”  

## Next

- [Intrusion analysis models](./2_Intrusion_Analysis_Models.md)

## Further reading

- [ODNI — ICD 203 Analytic Standards (PDF)](https://www.odni.gov/files/documents/ICD/ICD-203.pdf)
- [CIA CSI — Psychology of Intelligence Analysis (Heuer)](https://www.cia.gov/resources/csi/books-monographs/psychology-of-intelligence-analysis-2/)
- [CIA CSI — A Tradecraft Primer (structured analytic techniques)](https://www.cia.gov/resources/csi/static/Tradecraft-Primer-apr09.pdf)
- [NIST SP 800-150 — Guide to Cyber Threat Information Sharing](https://csrc.nist.gov/pubs/sp/800/150/final)
- [CREST — What Is Cyber Threat Intelligence and How Is It Used? (PDF)](https://www.crest-approved.org/wp-content/uploads/2024/04/what-is-ctihow-is-it-used_2024-v2.pdf)
- [Tor Project — What Tor is and how it works](https://support.torproject.org/about-tor/how-tor-works/overview/)
- [Tor Project — Download Tor Browser](https://www.torproject.org/download/)
- [Tor Project — Downloading (mirrors, GetTor, signatures)](https://support.torproject.org/tor-browser/getting-started/downloading/)
- [Tor Project — Installing Tor Browser](https://support.torproject.org/tor-browser/getting-started/installing/)
- [Tor Project — Onion services in Tor Browser](https://support.torproject.org/tor-browser/features/onion-services/)
- [Tor Project — Talk about onion services (journalists, sources, privacy)](https://community.torproject.org/onion-services/talk/)
- [CISA — Defending Against Malicious Cyber Activity Originating from Tor (AA20-183a)](https://www.cisa.gov/news-events/cybersecurity-advisories/aa20-183a)
- [Foundations/19 — Threat intelligence literacy](../Foundations/19_Threat_Intelligence_Literacy.md)
