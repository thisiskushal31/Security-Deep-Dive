# SAML enterprise SSO

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

**SAML 2.0 (Security Assertion Markup Language)** is an OASIS standard for XML-based assertions and protocols widely used for **enterprise SSO (single sign-on)**. Many corporate IdPs (identity providers) still speak SAML to SaaS SPs (service providers) even when newer apps prefer [OIDC](./5_OpenID_Connect.md).

## Concept

### Players

| Role | SAML name | Job |
|------|-----------|-----|
| Identity Provider | **IdP** | Authenticates user; issues assertions |
| Service Provider | **SP** / RP (relying party) | Consumes assertion; creates local session |
| Assertion | SAML Assertion | XML statement: authentication, attributes, authorization decisions |

### Assertion types (core)

| Statement | Meaning |
|-----------|---------|
| **Authentication** | Subject authenticated by IdP at a time with a context |
| **Attribute** | Claims about the subject (email, groups, …) |
| **Authorization decision** | Permit/deny for a resource (less common in Web SSO) |

### Web Browser SSO (the profile you meet first)

```text
User ▶ SP ──redirect──▶ IdP (authenticate)
              │
              ▼
        SAML Response (assertion)
              │
              ▼
User ▶ SP validates assertion ──▶ SP session
```

Bindings move the message:

| Binding | Grain |
|---------|--------|
| **HTTP Redirect** | Short messages via URL (often AuthnRequest) |
| **HTTP POST** | Larger Responses via browser form POST |
| **Artifact** | Browser carries a handle; SP fetches assertion on **back channel** |

Artifact / back-channel patterns matter for injection resistance — see [FAL](./7_Federation_Assurance_And_RP_Validation.md).

## Advanced

### Metadata is the trust contract

| Exchange | Why |
|----------|-----|
| SP ↔ IdP **metadata** (entity IDs, endpoints, certificates) | Establishes where to send messages and how to verify |
| Signing keys / rollover | Expired or wrong cert = outage or forge window |
| NameID formats & attribute contracts | Break apps when IdP changes UID format |

Manual metadata exchange is common in enterprise; treat updates as change-controlled.

### Validation checklist (SP)

| Check | Failure if skipped |
|-------|--------------------|
| Signature over assertion / response as required | Forged SSO |
| `Audience` / `Recipient` / Destination | Token replay to another SP |
| Time conditions (`NotBefore` / `NotOnOrAfter`) | Stale assertion reuse |
| InResponseTo (where applicable) | Unsolicited / mismatched responses |
| Transport TLS (Transport Layer Security) | Downgrade / MITM (man-in-the-middle) on ACS (Assertion Consumer Service) |

OASIS security considerations call out unsigned assertions, insufficient audience restriction, and XML wrapping attacks — defend with strict schema-aware XML signature validation (library choice matters).

### SAML vs OIDC (when teams choose)

| Prefer SAML when | Prefer OIDC when |
|------------------|------------------|
| SaaS vendor only offers SAML | Modern app stacks / mobile / APIs |
| Existing IdP federation already SAML | JSON/JWT tooling and UserInfo |
| Browser workforce SSO is the whole story | You also need OAuth API access beside login |

Bridge pattern: IdP speaks SAML to legacy SP and OIDC to new apps — one identity store, two assertion formats.

### Logout and session reality

SAML Single Logout (SLO) is optional and fragile across SaaS. Program expectation: **SP-local logout + IdP session revoke runbooks** — [Defensive-Ops/10](../Defensive-Ops/10_Cloud_Identity_And_SaaS_IR.md).

## Apply

1. Inventory SAML SPs: ACS URLs, signing certs, metadata owners.  
2. Confirm every production SP rejects unsigned assertions (unless a rare documented exception).  
3. Verify audience restrictions match each SP entity ID.  
4. Tabletop key rollover: IdP signing cert expiry in 30 days.  
5. Document SLO support per SP — assume “local logout only” unless tested.

## Next

- [Federation assurance and RP validation](./7_Federation_Assurance_And_RP_Validation.md)

## Further reading

- [OASIS SAML 2.0 — Assertions and Protocols (Core)](https://docs.oasis-open.org/security/saml/v2.0/saml-core-2.0-os.pdf)
- [OASIS SAML 2.0 — Profiles](https://docs.oasis-open.org/security/saml/v2.0/saml-profiles-2.0-os.pdf)
- [OASIS SAML 2.0 — Bindings](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf)
- [OASIS SAML 2.0 — Security and Privacy Considerations](https://docs.oasis-open.org/security/saml/v2.0/saml-sec-consider-2.0-os.pdf)
