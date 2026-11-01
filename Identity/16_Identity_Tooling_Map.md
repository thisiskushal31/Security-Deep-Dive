# Identity tooling map

[← Identity](./README.md)

> **Progression:** concept → advanced → apply.

Program concepts live in notes [1](./1_Digital_Identity_And_Assurance_Levels.md)–[15](./15_Privileged_Access_And_IGA.md). **Install and first use** live in Tooling — **one product → one folder**. This map is how to pick a capability without duplicating trees.

Primary home: [Tooling Security/Identity](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity).  
OAuth protocol literacy (specs): [Tooling Specs-Standards/OAuth](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Specs-Standards/OAuth).

**Lab / authorized IdPs and directories only.** Do not point lab IdPs at production user stores without a written plan.

## By capability (open source first)

| Capability | Open source (install here) | Commercial / SaaS (link only) |
|------------|----------------------------|-------------------------------|
| **Full IdP / workforce IAM** | [Keycloak](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Keycloak) · [authentik](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/authentik) · [Zitadel](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Zitadel) · [Casdoor](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Casdoor) | Okta · Microsoft Entra ID · Ping Identity · Auth0 (Okta) |
| **Headless / API-first identity** | [Ory](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Ory) (Kratos + Hydra) · [SuperTokens](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/SuperTokens) · [Logto](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Logto) | Clerk · FusionAuth · Amazon Cognito |
| **Forward-auth / proxy SSO** | [Authelia](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Authelia) · [oauth2-proxy](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/oauth2-proxy) · [Pomerium](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Pomerium) | Many ZTNA vendors (commercial) |
| **OIDC broker (not full user mgmt)** | [Dex](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Dex) | — |
| **Linux / infra identity** | [Kanidm](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Kanidm) · [FreeIPA](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/FreeIPA) | Microsoft AD · Google Managed AD |
| **Workload identity** | [SPIRE](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/SPIRE) | Cloud workload identity (AWS/GCP/Azure) · SPIFFE-compatible commercial meshes |
| **Identity-aware access (SSH/K8s/DB)** | [Teleport](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Teleport) (Community) | Teleport Enterprise · StrongDM · Perimeter81-class |
| **Secrets / dynamic creds** | [OpenBao](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/OpenBao) | HashiCorp Vault · cloud secret managers |
| **AuthZ policy engine** | [OPA](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/OPA) · [Casbin](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/Casbin) | Cedar (lang) · commercial authZ SaaS |
| **IGA / provisioning** | [midPoint](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity/midPoint) | SailPoint · Saviynt · Okta Identity Governance |
| **PAM (human privilege)** | Limited pure-OSS; Teleport + OpenBao cover *parts* | CyberArk · Delinea · BeyondTrust |
| **ITDR / identity detection** | IdP logs → [Wazuh](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wazuh) / SIEM; emerging OSS experiments | Microsoft Defender for Identity / Entra ID Protection · CrowdStrike Falcon Identity · Okta ThreatInsight |

Pick **one** primary IdP for a lab estate; use Dex when you only need a broker in front of upstream IdPs (common for Kubernetes).

## How this maps to Identity notes

| Note | Tools you actually touch |
|------|--------------------------|
| [3 MFA / WebAuthn](./3_Authenticators_MFA_And_WebAuthn.md) | Keycloak / authentik / Authelia / Entra / Okta passkeys |
| [4–7 federation](./4_OAuth_Authorization.md) | Keycloak, authentik, Zitadel, Ory Hydra, Dex, Okta, Ping |
| [8 tokens](./8_Tokens_Sessions_And_Client_Patterns.md) | Ory, SuperTokens, Logto, BFF patterns |
| [11 workload](./11_Workload_And_Non_Human_Identity.md) | SPIRE, OpenBao, cloud WI |
| [13 SCIM / directories](./13_Lifecycle_SCIM_And_Directories.md) | FreeIPA, Kanidm, midPoint, IdP SCIM |
| [14 authZ](./14_Authorization_Beyond_Tokens.md) | OPA, Casbin, Ory Keto (via Ory docs) |
| [15 PAM / IGA](./15_Privileged_Access_And_IGA.md) | Teleport, midPoint, CyberArk/SailPoint (commercial) |

## Apply

1. Choose one OSS IdP for a personal lab (Keycloak *or* authentik is enough to start).  
2. Protect one app with OIDC; one legacy path with forward-auth (Authelia / oauth2-proxy).  
3. Add SPIRE *or* cloud workload identity for one service-to-service call.  
4. Ship IdP login/admin logs into your lab SIEM.  
5. Only then evaluate paid IdP/PAM/IGA — against gaps, not brochure feature counts.

## Further reading

- [Tooling Security/Identity](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity)
- [Keycloak](https://www.keycloak.org/) · [authentik](https://goauthentik.io/) · [Ory](https://www.ory.sh/) · [SPIFFE/SPIRE](https://spiffe.io/)
- [Okta docs](https://developer.okta.com/docs/) · [Microsoft Entra documentation](https://learn.microsoft.com/en-us/entra/) · [SailPoint](https://documentation.sailpoint.com/) · [CyberArk docs](https://docs.cyberark.com/)
