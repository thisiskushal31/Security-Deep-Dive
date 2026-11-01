# People, org, and identity surface

[← Reconnaissance](./README.md)

> **Progression:** concept → advanced → apply.

Technical recon finds machines; **identity recon** finds how humans and IdPs (Identity Providers) are exposed publicly. Attackers use this for phishing, password spraying, and trust abuse. Defenders use it for awareness, DMARC (Domain-based Message Authentication, Reporting and Conformance), and SSO (single sign-on) hardening. Identity program depth: [Identity](../Identity/README.md). Collectors often feed email lists: [theHarvester](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/theHarvester).

## Concept

### What gets collected

| Artifact | Attacker use | Defender use |
|----------|--------------|--------------|
| Email formats (`j.smith@`) | Guessable addresses | Monitor; don’t publish full directories |
| Org charts / LinkedIn | Role targeting | Limit sensitive role detail |
| Password dump hits | Spray / credential stuffing | Reset; MFA; detection |
| IdP discovery (`login.`, `sso.`) | Phish kits / AitM (adversary-in-the-middle) prep | Conditional access; brand monitoring |
| Helpdesk / VPN docs | Social engineering scripts | Need-to-know; watermark |
| Public calendars / speakers | Timing / pretext | Reduce oversharing |

```text
Public people data + login surfaces
            │
            ▼
   phishing / spray / helpdesk paths
            │
            ▼
   MFA, DMARC, CA policies, training
```

### Boundaries (clear, not theatrical)

| Allowed in most labs / awareness | Requires explicit RoE |
|----------------------------------|------------------------|
| Studying *your* public email pattern | Phishing real employees |
| Noting that Okta/Azure AD login is internet-facing | SIM-swap / pretexting |
| Checking if your domain fails DMARC | Sending mail as the domain |
| Reading public job posts for stack clues | Impersonating recruiters |

## Advanced

### How email recon actually works

1. Collect candidates (site, Git commits, collectors).  
2. Infer pattern from a few known addresses.  
3. Generate guesses from name lists.  
4. Validate via **permitted** methods only (e.g. lab mail servers) — many “validators” abuse third-party APIs.

Defense: catch-all carefully; monitoring; don’t expose global address lists; phishing-resistant MFA.

### Mail authentication as recon signal *and* control

| Record | Recon signal | Control goal |
|--------|--------------|--------------|
| SPF | Who may send as you | Tight `include:` / `-all` |
| DKIM | Signing presence | Keys rotated; selectors sane |
| DMARC | Policy strength (`none` vs `reject`) | Move toward enforcement with monitoring |

Failed DMARC is both an attacker opportunity and a defender ticket.

### IdP and auth surface recon

| Finding | Implication | Defense |
|---------|-------------|---------|
| Predictable `sso.company.com` | Easy to spoof in phish | User training + brand indicators + CA |
| Password reset that leaks validity | User enumeration | Generic responses |
| Legacy IMAP/POP / basic auth | Spray paradise | Block basic auth; modern auth only |
| Federation metadata public | Often expected | Still inventory; watch for rogue apps |
| “Remember this device” sprawl | Session theft impact | Device compliance; short sessions |

### Org / supply chain adjacency

Job posts naming “Palo Alto + Okta + Snowflake,” vendor portals, and partner extranets expand the **trust graph**. Recon of *your* third parties belongs in vendor risk — [GRC](../GRC/README.md) — not drive-by scanning partners.

### ATT&CK mapping

Gather victim identity information (T1589), phish for information (T1598). Purple: test detection of spray / suspicious IdP logins after disclosed recon — not unsolicited phishing.

## Apply

1. For your domain: document public email pattern (if any) and SPF/DKIM/DMARC status.  
2. List every public login URL you can find passively.  
3. One control each: mail auth, MFA policy, reduce public role maps.  
4. Decide what employee data your collectors would retain — minimize it.

## Further reading

- [CISA — Phishing Guidance](https://www.cisa.gov/phishing)
- [M3AAWG — Email authentication](https://www.m3aawg.org/)
- [MITRE ATT&CK — Gather Victim Identity Information (T1589)](https://attack.mitre.org/techniques/T1589/)
- [Identity](../Identity/README.md)
- [DMARC.org](https://dmarc.org/)
