# AAA and access fundamentals

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Access control rests on three linked jobs: prove identity, decide permissions, and record activity.

## Concept

## AAA

| Job | Question | Failure mode |
|-----|----------|--------------|
| Authentication | Who or what is making the request? | Account takeover; spoofed workload |
| Authorization | What may that identity do on this resource? | Insecure direct object reference; accidental admin |
| Accounting | What happened, when, and by whom? | No investigation trail; weak non-repudiation |

Authentication without authorization checks is how broken access control stays common. Privileged paths without accounting are blind.

```text
Identify  →  Authenticate  →  Authorize  →  Account (audit)
```

### Common AAA protocol families (literacy)

| Family | Role (grain) |
|--------|----------------|
| RADIUS | Centralized authentication/authorization/accounting for network access (VPN, Wi-Fi, NAS) |
| TACACS+ | Device administration AAA (often separate authZ for network device commands) |
| Kerberos | Ticket-based authentication inside many enterprise domains |
| Modern app federation | OAuth 2 / OIDC / SAML for applications — [Identity/1](../Identity/1_OAuth_OIDC_SAML.md) |

Protocol packet detail stays in Networks. Application federation how-to stays in Identity and Tooling.

## Authenticating people

| Factor class | Examples |
|--------------|----------|
| Something you know | Password, PIN, passphrase |
| Something you have | Hardware key, phone authenticator, smart card |
| Something you are | Biometrics |
| Context (supplement) | Time window, network location / geofence, device posture — useful signals, usually not a sole factor |

Multi-factor authentication uses at least two different classes. Phishing-resistant methods (for example WebAuthn / hardware-backed passkeys) resist common real-time phishing better than SMS one-time codes alone. Shared “team passwords” are not authentication.

### Credential hygiene (literacy)

| Prefer | Avoid |
|--------|-------|
| Longer passphrases or manager-generated secrets | Short, reused, or dictionary passwords |
| Unique secret per service | Same password across work and personal |
| Password manager + MFA | Secrets in chat, tickets, or screenshots |
| Length over clever complexity theater | Writing secrets on sticky notes next to the laptop |

### SSO and federation (literacy)

**Single sign-on (SSO)** authenticates once into an identity provider, then grants access to many apps via federation. Strength comes from the IdP (MFA, logging, lifecycle). Weakness is concentration: IdP compromise or mis-federation affects many apps. Protocol how-to: [Identity/1](../Identity/1_OAuth_OIDC_SAML.md).

## Authenticating systems and workloads

| Pattern | Practice |
|---------|----------|
| Static API keys | Short-lived where possible; rotate; never commit to git |
| Mutual TLS | Strong peer authentication; certificate lifecycle cost |
| Workload identity / federation to cloud | Prefer over long-lived keys on CI runners and nodes |

Secrets practice in delivery: [DevOps-Handbook Security/1](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/1_Security_Practices_And_Secrets.md). CI cloud identity: [Security/5](https://github.com/thisiskushal31/DevOps-Handbook/blob/main/Security/5_OIDC_CI_And_Least_Privilege.md).

## Advanced

## Authorization models

| Model | Idea | Typical use |
|-------|------|-------------|
| Discretionary (DAC) | Owner-controlled access lists | Classic filesystems |
| Mandatory (MAC) | System-enforced labels / clearances | High-assurance environments |
| Role-based (RBAC) | Roles map to permissions | Most business applications |
| Attribute-based (ABAC) | Policy over user, resource, and context attributes | Fine-grained and Zero Trust-style policy engines |
| Relationship-based | Permissions from relationships in a graph | “Shared with me” style access |

**Object-level authorization** checks the specific resource, not only the caller’s role. Role `user` plus `GET /orders/123` still requires that this user may see order `123`. API detail: [AppSec/2](../AppSec/2_API_Security.md).

### Access control matrix (literacy)

| | Object A (payroll DB) | Object B (public wiki) |
|--|----------------------|------------------------|
| Alice (HR) | read, write | read |
| Bob (eng) | — | read, write |
| Carol (admin) | read | read, write |

Rows are subjects; columns are objects; cells are operations. Real systems implement this idea with ACLs, capabilities, RBAC, or ABAC — the matrix explains why “everyone is admin” does not scale.

| Pattern | Meaning |
|---------|---------|
| Least privilege | Minimal rights for the task |
| Just-in-time elevation | Temporary higher rights with approval and expiry |
| Break-glass | Emergency access; logged and reviewed |
| Separation of duties | Split sensitive end-to-end workflows |

## Accounting

| Need | Practice |
|------|----------|
| Who / what / when / where | Subject, action, object, time, source |
| Log integrity | Restricted write access; retention; forward to a collector |
| Privileged actions | Always logged; alert on anomalies |
| Time sync | Reliable timestamps for investigation |

Accounting feeds [Defensive-Ops](../Defensive-Ops/README.md) and supports non-repudiation ([1](./1_CIA_And_Risk.md)).

## Access gap analysis

Useful questions: password-only entry points still left; shared admin accounts; workloads with unbounded cloud permissions; missing MFA on identity providers and remote access. Turn gaps into an owned backlog.

## Related homes

- OAuth / OIDC / SAML → [Identity/1](../Identity/1_OAuth_OIDC_SAML.md)
- Zero Trust → [13](./13_Zero_Trust_And_Deception_Basics.md) · [Identity/2](../Identity/2_Zero_Trust.md)
- Session and service-identity trade-offs → [System-Design Security-Tradeoffs/2](https://github.com/thisiskushal31/System-Design-Concepts/blob/main/Security-Tradeoffs/2_Auth_Design_vs_Zero_Trust.md)

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] Define authentication, authorization, and accounting without circular wording  
- [ ] Name three authorization models and one fit for each  
- [ ] Explain object-level authorization vs role-only checks  
- [ ] Sketch a three-subject access matrix for one sensitive object  
- [ ] State one benefit and one concentration risk of SSO  

## Next

- [Cryptographic solutions literacy](./8_Cryptographic_Solutions_Literacy.md)

## Further reading

- [NIST SP 800-63B — Digital Identity Guidelines (Authentication)](https://pages.nist.gov/800-63-3/sp800-63b.html)
- [NIST SP 800-53 Rev. 5 — AC family overview](https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final)
- [RFC 2865 — RADIUS](https://www.rfc-editor.org/rfc/rfc2865)
- [RFC 4120 — The Kerberos Network Authentication Service (V5)](https://www.rfc-editor.org/rfc/rfc4120)
