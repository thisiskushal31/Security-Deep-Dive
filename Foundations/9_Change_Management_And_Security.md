# Change management and security

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Many outages and exposures arrive as changes: a firewall rule, an identity grant, a dependency bump, or a “quick” production edit. Security-aware change management makes those changes deliberate, reviewable, and reversible.

Delivery mechanics (pipelines, branching, approvals) live in DevOps-Handbook `CiCd/` and `Methodologies/`. This note is the security impact of change.

## Concept

## Why change is a security event

| Uncontrolled change | What goes wrong |
|---------------------|-----------------|
| Temporary open network rule that never expires | Permanent exposure |
| Undocumented production config edit | No safe rollback; weak investigation |
| Unreviewed dependency bump | Known vulnerable code enters the build |
| Control disabled to “make it work” | Silent loss of MFA, filtering, or logging |

## Process elements that matter

| Element | Security question |
|---------|-------------------|
| Request and approval | Do sensitive changes need the right approvers (separation of duties)? |
| Ownership | Who owns the system and who owns this change? |
| Stakeholders | Do security, operations, and data owners need a say when class or exposure changes? |
| Impact analysis | What happens to confidentiality, integrity, availability, and blast radius? |
| Test evidence | What proof exists before production (including automated gates)? |
| Backout plan | How do you undo the change, and who can run that plan? |
| Maintenance window | When are fragile changes allowed? |
| Standard procedure | Is the path repeatable, or only tribal knowledge? |

## Advanced

## Technical implications to check

| Implication | Ask |
|-------------|-----|
| Allow lists / deny lists | Did trust widen by accident? |
| Restricted activities | Is this change forbidden in production without break-glass? |
| Downtime | Availability impact and communications? |
| Service or application restart | Session loss; fail-open vs fail-closed behavior? |
| Legacy systems | Does this bypass SSO, logging, or policy enforcement? |
| Dependencies | Downstream break; certificate or protocol mismatch? |

## Documentation and version control

| Artifact | Update when |
|----------|-------------|
| Architecture and network diagrams | Trust boundaries or data flows change |
| Runbooks and procedures | The operating steps change |
| Policies and standards | Expected controls change |
| Infrastructure and pipeline config | Prefer git as the source of truth |

Version control answers who changed what and whether it was reviewed. Console-only changes without drift detection are a cloud posture problem: [Cloud-Security](../Cloud-Security/README.md).

## Security checkpoints in delivery

Do not duplicate the gate chain here. Use:

- [Security gate chain](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/4_Security_Gate_Chain.md)
- Threat model on a **new trust boundary** ([12](./12_STRIDE_And_Threat_Modeling.md))
- Secret scanning, SAST, SCA, IaC policy, signing, and DAST as the change warrants

```text
Propose change → impact + approval → test/gates → deploy → verify → update docs
                 └────────── backout plan ready ──────────┘
```

## Emergency change

| Rule | Practice |
|------|----------|
| Still logged | Create or complete the record after the fact |
| Time-bounded | Elevated access and exceptions expire |
| Reviewed | Post-change or incident review |
| Restored | Return to the normal control state |

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] List five process elements and one security question for each  
- [ ] Explain why temporary firewall holes need expiry  
- [ ] Point to where pipeline gates enforce change quality in this series  

## Next

- [Secure design principles](./10_Secure_Design_Principles.md)

## Further reading

- [NIST SP 800-128 — Guide for Security-Focused Configuration Management of Information Systems](https://csrc.nist.gov/pubs/sp/800/128/final)
- [DevOps-Handbook Security gate chain](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/4_Security_Gate_Chain.md)
