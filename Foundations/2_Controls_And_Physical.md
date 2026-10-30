# Controls, physical security, and deception

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

A **control** is a safeguard or countermeasure that reduces risk. Classify controls so you do not treat a camera as authorization, or a policy document as encryption.

## Concept

## Categories (by nature)

| Category | Meaning | Examples |
|----------|---------|----------|
| Technical | Enforced by hardware or software | MFA, encryption, EDR, WAF, secure configuration |
| Managerial | Policy, standards, and governance | Access reviews, risk policy, secure development standards |
| Operational | Day-to-day procedures people run | Patch cadence, restore drills, incident playbooks |
| Physical | Site, building, and media | Badges, locks, cages, cameras, secure disposal |

Real programs usually need more than one category for the same risk (policy requires MFA; the identity system enforces it; operations review coverage).

## Types (by function)

| Type | Job | Examples |
|------|-----|----------|
| Preventive | Stop the event | Least privilege; input validation; allow lists; segmentation |
| Detective | Notice the event | Audit logs; SIEM alerts; integrity monitoring |
| Corrective | Limit damage and restore | Incident response; key revocation; restore from backup |
| Deterrent | Discourage attempts | Warning banners; visible cameras (weak alone) |
| Compensating | Alternate when the preferred control is not yet possible | Extra monitoring while a legacy system cannot be patched |
| Directive | Instruct required behavior | Policies; standards; acceptable-use rules; runbooks |

A healthy mix is **preventive + detective + corrective**. Detection without response is only a dashboard.

```text
Risk scenario
  → Preventive (stop)
  → Detective (see)
  → Corrective (recover)
  → Directive (tell people the rule)
```

### One risk, several controls

| Risk | Preventive | Detective | Corrective |
|------|------------|-----------|------------|
| Stolen cloud credential | Short-lived federation; least privilege | Anomalous API alerts | Rotate and revoke; incident process |
| Ransomware | Offline/immutable backups; least privilege | Endpoint detection; unusual encrypt behavior | Restore; rebuild |
| Injection in an app | Parameterized queries; safe frameworks | WAF and application logs | Patch; rotate database credentials |

## Principles that show up in almost every design

| Principle | Meaning |
|-----------|---------|
| Least privilege | Only the rights needed for the job — for people and for workloads |
| Need to know | Data access limited by duty, not only by job title |
| Separation of duties | No single person completes a sensitive end-to-end path alone |
| Defense in depth | Layered controls; assume one layer fails |
| Fail secure | On failure, prefer deny unless a documented availability design requires otherwise |

Zero Trust as a strategy: [13](./13_Zero_Trust_And_Deception_Basics.md).

## Defense in depth — layers to name

```text
Governance / policy
  Identity and access
    Network / edge
      Host / runtime
        Application
          Data
            Detect → Respond → Recover
```

Wire depth: Networks-Deep-Dive. Delivery gates: DevOps-Handbook Security. Detection and response: [Defensive-Ops](../Defensive-Ops/README.md). Defensible network literacy: [14](./14_Defensible_Network_Architecture_Literacy.md).

## Advanced

## NIST CSF functions (map controls to outcomes)

A control catalog answers “what safeguard?” The [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework) answers “toward which outcome?”

| Function | Outcome focus | Example controls |
|----------|---------------|------------------|
| **Govern** | Strategy, roles, policy, oversight | Program policy; risk appetite; roles ([4](./4_Security_Policy_Governance_And_Roles.md)) |
| **Identify** | Know assets, risks, suppliers | Inventory; BIA; threat modeling ([6](./6_Assets_Data_States_And_Assurance.md), [12](./12_STRIDE_And_Threat_Modeling.md)) |
| **Protect** | Limit likelihood and impact | MFA; encryption; hardening; segmentation |
| **Detect** | Find anomalies and events | Logging; SIEM; decoys |
| **Respond** | Contain and eradicate | Incident process ([Defensive-Ops/2](../Defensive-Ops/2_Incident_Response.md)) |
| **Recover** | Restore operations and improve | Backups; DR drills ([21](./21_Resilience_Continuity_And_Recovery_Literacy.md)) |

Use categories/types to classify a control; use CSF functions to check the program is not “all Protect, no Detect/Recover.”

## Physical security (literacy)

| Control | Purpose |
|---------|---------|
| Fencing / barriers | Delay and deter perimeter intrusion |
| Bollards | Reduce vehicle ramming into entrances |
| Access control vestibule | One-person entry; reduce tailgating |
| Badges, locks, visitor process | Authenticate entry; escort where required |
| Guards | Adapt when sensors are not enough |
| Lighting | Support cameras; deter |
| Video surveillance | Detect, deter, and support investigation — not a substitute for logical access control |
| Sensors (for example infrared, pressure, microwave, ultrasonic) | Alarm paths for intrusion |
| Media and device control | Limit removable media; wipe or destroy media leaving the site |
| Environmental controls | Fire, power, and cooling protect availability of the facility |

Secure the building and the cluster. Facility engineering detail lives in datacenter notes elsewhere in the series.

## Deception technology

Decoys detect and delay attackers. They do not replace patching or least privilege.

| Technology | Idea |
|------------|------|
| Honeypot | System that should never see legitimate users |
| Honeynet | Network of decoys |
| Honeyfile | File that should never be opened by normal work |
| Honeytoken | Credential or token that should never be used |

Alert when a decoy is touched; route into [Defensive-Ops](../Defensive-Ops/README.md). Use only on assets you own. Label clearly so responders do not treat decoys as production.

## Apply
1. Start from risk and data class, not from a product brochure.  
2. Prefer preventive controls when they are cheap and reliable.  
3. Pair material residual risk with detective and corrective controls.  
4. Prefer standard controls (MFA, TLS, least privilege) before exotic ones.  
5. Measure coverage, false positives, time to detect, and time to recover.  
6. Give compensating controls an expiry and a plan to remove them.

## Related homes

- Networks security → [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive)
- Pipeline and secrets → [DevOps-Handbook Security](https://github.com/thisiskushal31/DevOps-Handbook/tree/main/Security)
- Mitigations catalog → [Threats/7](../Threats/7_Mitigations.md)

## Self-check

- [ ] Give one example each for technical, managerial, operational, and physical  
- [ ] Give one example each for preventive, detective, corrective, compensating, and directive  
- [ ] Explain why badge plus camera is not database authorization  
- [ ] Map one control to a CSF function and to a category/type  

## Next

- [Ethics, authorization, and lab rules](./3_Ethics_Authorization_And_Lab_Rules.md)

## Further reading

- [NIST SP 800-53 Rev. 5 — Security and Privacy Controls](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final)
- [NIST SP 800-12 Rev. 1 — An Introduction to Information Security](https://csrc.nist.gov/pubs/sp/800/12/r1/final)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
