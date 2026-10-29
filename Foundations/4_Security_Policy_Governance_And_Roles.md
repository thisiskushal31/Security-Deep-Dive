# Security policy, governance, and roles

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Security only works when expectations are written, owned, and tied to the mission. This note covers policy hierarchy, governance literacy, and the technical roles that show up in a security program. Framework catalogs and audit reports: [GRC](../GRC/README.md).

## Concept

## Security supports the mission

Information security exists to enable the organization’s mission under risk — not as an end in itself. Protections should be **commensurate with risk**, comprehensive enough to cover dependencies, and reviewed regularly. Societal and legal constraints (privacy, acceptable monitoring) bound what controls are appropriate.

## Policy hierarchy

| Layer | Role |
|-------|------|
| **Program policy** | High-level organizational commitment: purpose, scope, responsibilities, compliance |
| **Issue-specific policy** | Focused rules for a technology or concern (acceptable use, remote access, removable media) |
| **System-specific policy** | Rules and required configurations for a particular system |

Supporting artifacts (more detailed than policy):

| Artifact | Meaning |
|----------|---------|
| **Standard** | Mandatory methods or technologies |
| **Guideline** | Recommended approaches |
| **Procedure** | Step-by-step how to perform a task |

```text
Program policy
  → Issue-specific policies
    → System-specific policies
      → Standards / guidelines / procedures
```

Vague policy with no procedures produces inconsistent controls. Procedures with no policy produce busywork without authority.

## Advanced

## Due care and due diligence

| Term | Meaning |
|------|---------|
| Due care | Taking reasonable steps to protect assets and people (the duty) |
| Due diligence | The ongoing investigation and verification that those steps are actually working |

Evidence of both often includes policies, risk decisions, monitoring results, and corrective actions.

## Roles (literacy)

Names vary by org size. The functions matter more than the title:

| Function | Focus |
|----------|-------|
| Senior leadership / risk executive | Mission risk appetite; resource decisions |
| Authorizing official (or equivalent) | Accepts residual risk to operate a system |
| System owner | Owns the system’s mission fit, operation, and security outcomes |
| Information owner / steward | Owns data classification and sharing rules |
| Security officer / security program lead | Runs or coordinates the security program |
| Security architect / engineer | Designs and implements security into systems |
| Control assessor | Independently tests whether controls work |
| Administrator / operator | Day-to-day configuration and operations |
| User | Follows policy; reports issues |

Common controls (shared across systems) need a clear provider so system owners do not assume “someone else” secured the identity platform or logging stack.

## Governance principles (technical)

| Principle | Practice |
|-----------|----------|
| Align security to mission and objectives | Prioritize controls that protect critical services and data |
| Explicit roles | Every system and common control has an owner |
| Control frameworks | Use a catalog (for example NIST SP 800-53, ISO/IEC 27001 Annex A) via [GRC](../GRC/README.md) — do not invent a private taxonomy without mapping |
| Measure and monitor | Assessment and continuous monitoring close the loop ([5](./5_Risk_Management_Process_And_RMF.md)) |

## Law and policy (literacy, not legal advice)

| Layer | What builders need to know |
|-------|----------------------------|
| Contract / AUP | What users and vendors are allowed to do |
| Sector rules | Extra duties for health, finance, government, kids’ data where they apply |
| Breach / incident duties | Who must be notified and on what clock — coordinate with counsel and IR |
| Evidence / monitoring limits | Logging and investigation must stay within policy and law |

Catalog frameworks and attestations: [GRC](../GRC/README.md). Human-factor abuse (phishing, pretexting): [16](./16_Human_Security_Literacy.md).

## Privacy and security (boundary)

Security protects CIA (and related properties). Privacy governs lawful, fair, and proportionate use of personal data. Overlap is large (access control, encryption, minimization), but privacy also includes purpose limitation, retention, and individual rights. Treat privacy requirements as design inputs, not an afterthought bolted onto encryption alone. Foundation detail: [17](./17_Privacy_Literacy.md).

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] Distinguish program, issue-specific, and system-specific policy  
- [ ] Distinguish standard, guideline, and procedure  
- [ ] Explain due care vs due diligence in one sentence each  
- [ ] Name system owner vs information owner vs authorizing official functions  

## Next

- [Risk management process and RMF](./5_Risk_Management_Process_And_RMF.md)

## Further reading

- [NIST SP 800-12 Rev. 1 — An Introduction to Information Security](https://csrc.nist.gov/pubs/sp/800/12/r1/final) (policy and roles chapters)
- [NIST Risk Management Framework](https://csrc.nist.gov/projects/risk-management)
- [ISC2 CISSP Exam Outline — Domain 1 (Security and Risk Management)](https://www.isc2.org/certifications/cissp/cissp-certification-exam-outline)
