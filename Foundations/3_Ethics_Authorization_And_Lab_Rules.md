# Ethics, authorization, and lab rules

[← Foundations](./README.md)

> **Progression:** concept → advanced → apply. *Apply* here means foundation literacy and builder habits — not a specialty lab.

Every offensive, purple, and many defensive practice paths start with the same rule: **authorization first**. This note is the foundation those courses assume before tools and exploits.

Pentest phases and reporting: [Offensive](../Offensive/README.md). Tool installs: [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security).

## Concept

## Authorization

| Situation | Requirement |
|-----------|-------------|
| Employer / client engagement | Written rules of engagement (scope, timing, allowed techniques, contacts) |
| Bug bounty | Program rules and in-scope assets only |
| Personal learning | Systems you own, or platforms that explicitly grant lab permission |
| “Just a quick scan” of a random host | Not authorized |

No ticket, no test. Scanning or exploiting systems without permission can be a crime regardless of intent.

## Pentest methodology literacy (bridge)

Foundations stops at authorization and shape. Phase detail lives in [Offensive](../Offensive/README.md).

| Shape | What it is |
|-------|------------|
| PTES-like flow | Pre-engagement → intelligence → threat modeling / discovery → vulnerability analysis → exploitation → post-exploitation → reporting |
| OWASP Testing Guide | Web/application-focused testing guidance |
| NIST SP 800-115 | Technical assessment / testing techniques framing |

All of them share: **authorize → discover → validate → report**.

## Advanced

## Rules of engagement (minimum fields)

| Field | Why |
|-------|-----|
| In-scope hosts, apps, accounts | Prevent collateral damage |
| Out-of-scope systems | Third parties, production DR, personal devices |
| Allowed and forbidden techniques | For example no DoS unless explicitly allowed |
| Time windows | Avoid critical business moments |
| Emergency stop contacts | Who to call if something breaks |
| Data handling | How findings and screenshots are stored and deleted |

## Lab practice norms

| Do | Don't |
|----|-------|
| Isolated VMs, intentionally vulnerable labs, authorized platforms | Attack café Wi-Fi, neighbors, or random internet hosts |
| Snapshot and revert | Leave persistence on shared lab gear without cleanup |
| Document detections for purple learning | Publish exploit recipes aimed at live third parties |
| Prefer understanding classes of issues | Copy-paste unexplained payloads into environments you do not own |

## Ethics for builders and defenders

| Habit | Why |
|-------|-----|
| Minimize data collected in tests | Least privilege for evidence |
| Report responsibly through agreed channels | Reduce harm |
| Do not dox or harass | OSINT has boundaries |
| Separate work and personal tooling accounts | Reduce accidental cross-contamination |

## Event handling honesty

Security events can affect people and customers. Coordinate with policy and counsel when disclosure or legal holds apply — [GRC](../GRC/README.md) and [Defensive-Ops IR](../Defensive-Ops/2_Incident_Response.md).

## Apply

Use the self-check below as the apply gate for this note.

## Self-check

- [ ] State the authorization rule in one sentence  
- [ ] List five RoE fields  
- [ ] Name three acceptable lab targets and three that are not  

## Next

- [Security policy, governance, and roles](./4_Security_Policy_Governance_And_Roles.md)

## Further reading

- [PTES — The Penetration Testing Execution Standard](http://www.pentest-standard.org/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [Security-Deep-Dive Offensive methodology](https://github.com/thisiskushal31/Security-Deep-Dive/tree/main/Offensive)
