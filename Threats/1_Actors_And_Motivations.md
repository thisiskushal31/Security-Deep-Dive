# Actors and motivations

[← Threats](./README.md)

Who might harm your assets, and why. Motivation shapes likelihood and which controls matter first.

## Common actor classes

| Actor | Typical motivation | Notes |
|-------|--------------------|-------|
| **Opportunistic / script kiddie** | Status, curiosity, easy wins | Automated scans; unpatched internet-facing apps |
| **Cybercrime** | Money (ransomware, fraud, resale of access) | High volume; affiliate models |
| **Insider** (malicious or negligent) | Revenge, profit, or mistakes | Already past the perimeter |
| **Competitor / industrial** | Advantage, IP theft | Targeted; slower |
| **Hacktivist** | Ideology, publicity | Defacement, leaks, DDoS |
| **Nation-state / APT** | Espionage, disruption, pre-positioning | Patient; custom tooling; supply chain interest |

Labels overlap. A “crime” crew may use nation-state-grade tooling sold as a service.

## Motivation → what you prioritize

| Motivation | First-line focus |
|------------|------------------|
| Money | Backup/restore drills, ransomware path, payment fraud controls |
| Espionage | Data classification, egress monitoring, insider risk |
| Disruption | Availability (capacity, DDoS, blast radius) |
| Accidental insider | Least privilege, change control, safe defaults |

Map concrete techniques later with [ATT&CK in GRC](../GRC/1_NIST_ISO_And_ATTCK.md). Do not invent a unique actor taxonomy per team if ATT&CK groups already work.

## Next

- [Vectors and surfaces](./2_Vectors_And_Surfaces.md)
