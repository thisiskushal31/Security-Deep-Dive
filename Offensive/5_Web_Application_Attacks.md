# Web application attacks (issue classes)

[← Offensive](./README.md)

How testers **think about** web risk. Practice on intentionally vulnerable labs (DVWA, Juice Shop, PortSwigger Academy, HTB web boxes). Defense mapping: [AppSec](../AppSec/README.md).

## Classes (not exploit recipes)

| Class | What goes wrong | Defender grain |
|-------|-----------------|----------------|
| Injection | Untrusted input becomes code/query | Parameterize; encode |
| Broken auth / session | Weak login, fixation, logout fails | OIDC/MFA; secure cookies |
| Broken access control | IDOR, privilege climb | Object-level authZ |
| XSS | Script in victim browser | Encode; CSP |
| CSRF | State change without intent | Tokens; SameSite |
| SSRF | Server fetches attacker URL | Egress allowlists |
| Misconfig | Default creds, directory listing | Harden; CSPM |

## Proxy workflow

Intercept and replay in a lab with [Burp](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Offensive/Burp_Suite) or [Caido](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Offensive/Caido); automated DAST with [ZAP](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Offensive/OWASP_ZAP) / Handbook ZAP.

## Next

- [Exploitation (lab only)](./6_Exploitation_Lab_Only.md)
