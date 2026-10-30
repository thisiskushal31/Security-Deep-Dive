# Human security literacy

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Most successful intrusions still start with people: phishing, pretexting, helpdesk abuse, or rushed clicks under pressure. This note is foundation vocabulary for Security+, GSEC-style essentials, and CSEC “human security” — not a full social-engineering tradecraft course.

Vectors catalog: [Threats/2](../Threats/2_Vectors_And_Surfaces.md). Ethics boundary: [3](./3_Ethics_Authorization_And_Lab_Rules.md).

## Concept

## Why humans are in scope

| Reality | Implication |
|---------|-------------|
| People hold credentials, approve access, and move money | Identity and process are attack surfaces |
| Urgency and authority bias work | Attacks abuse emotion and hierarchy |
| Training alone is weak | Pair awareness with technical controls (MFA, reporting, least privilege) |

## Common techniques (literacy)

| Technique | Idea |
|-----------|------|
| Phishing / spear phishing | Fraudulent messages that trick users into credentials, malware, or money movement |
| Smishing / vishing | Same ideas over SMS or voice |
| Pretexting | Invented scenario to extract information or action |
| Baiting | Physical or digital lure (USB, “free” download) |
| Tailgating / piggybacking | Follow authorized people through physical controls |
| Business email compromise (BEC) | Impersonate executives or vendors to redirect payments or data |
| Helpdesk / MFA fatigue abuse | Socially engineer reset or approval flows |
| Deepfake / voice-clone assisted fraud | Synthetic audio/video that mimics a trusted person — verify out-of-band; see [Threats/8](../Threats/8_AI_Era_Threats.md) |

```text
Lure → trust / urgency → action (click, send, approve)
         ↓
   Prefer: report → verify out-of-band → technical block
```

## Advanced

## Defenses that belong at foundation level

| Control | Role |
|---------|------|
| Phishing-resistant MFA | Reduce value of stolen passwords |
| Easy report button + fast triage | Turn users into sensors |
| Out-of-band verification for money / access changes | Break BEC |
| Least privilege and just-in-time admin | Limit blast radius after a click |
| Simulated phishing with coaching | Practice — not punishment theater |
| Clear helpdesk identity-proofing | Stop social-engineered resets |

## Awareness vs blame

Awareness training is a **directive** control. If users are routinely defeated, also fix mail filters, MFA, privilege, and processes. Blaming individuals without fixing systems is not a security program.

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] Name five human-centric techniques and one control that reduces each  
- [ ] Explain why MFA + report path beats awareness slides alone  
- [ ] State one helpdesk process that resists pretexting  

## Next

- [Privacy literacy](./17_Privacy_Literacy.md)

## Further reading

- [CISA — Phishing Guidance](https://www.cisa.gov/phishing)
- [NIST SP 800-50 — Building an Information Technology Security Awareness and Training Program](https://csrc.nist.gov/pubs/sp/800/50/final)
- [Threats — vectors and surfaces](../Threats/2_Vectors_And_Surfaces.md)
- [Threats — AI-era threats](../Threats/8_AI_Era_Threats.md)
- [CISA — Risk in Focus: Generative AI and the 2024 Election Cycle](https://www.cisa.gov/sites/default/files/2024-05/Consolidated_Risk_in_Focus_Gen_AI_ElectionsV2_508c.pdf)
- [CCCS — Top 10 AI security actions](https://www.cyber.gc.ca/en/guidance/top-10-artificial-intelligence-security-actions-primer-itsap10049) (deepfake / impersonation actions)
