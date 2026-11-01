# Web AppSec labs

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Web practice should teach **defect classes** (injection, XSS, access control, …) against authorized labs — then map back to [AppSec](../AppSec/README.md). Prefer free interactive academies and local OWASP apps you isolate ([3](./3_Local_Lab_Hygiene.md)).

## Basic apply

### Path A — PortSwigger Web Security Academy (browser labs)

1. Open the [Web Security Academy](https://portswigger.net/web-security).  
2. Pick one topic (e.g. SQL injection or XSS) at **Apprentice**.  
3. Read the topic text, then complete the lab in their environment.  
4. Note: vulnerability class → payload idea → why the fix works (your words).  
5. Install Burp for your own local targets later — [Tooling](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security).

### Path B — Local Juice Shop (modern app + scoreboard)

```text
Host browser ──proxy optional──▶ 127.0.0.1:3000 Juice Shop
                                      │
                                      ▼
                               Scoreboard challenges
```

1. Run Juice Shop bound to localhost (official image / docs).  
2. Create a throwaway login; open the scoreboard.  
3. Solve **easy** challenges without spoilers first.  
4. After each solve: write the OWASP-style class in your notes.  
5. Tear down the container when done.

### Path C — DVWA difficulty ladder

1. Deploy DVWA locally (isolated).  
2. Set security to **low**; solve one module (e.g. SQLi).  
3. Raise to **medium**, then **high** for the *same* module.  
4. Record how defenses changed your approach — that is the lesson.

WebGoat is another deliberate Java teaching app — use when you want structured lessons inside the app UI.

## Advanced apply

### Combine Academy + local app

| Week | Drill |
|------|-------|
| 1 | Academy Apprentice SQLi + DVWA SQLi low→medium |
| 2 | Academy XSS + Juice Shop XSS-family challenges |
| 3 | Access control / IDOR topic + Juice Shop authz challenges |
| 4 | Blind retry: new Juice Shop challenges with hints disabled |

### Add tooling deliberately

| Goal | Tool home |
|------|-----------|
| Intercept / replay | Burp or ZAP in Tooling |
| Content discovery | ffuf / Gobuster (lab targets only) |
| Report the class | AppSec Top 10 / ASVS notes |

### Secrets and CIAM side quests

OWASP WrongSecrets practices finding secrets in apps/cloud-shaped setups — still only in **your** lab. Pair with [Cryptography](../Cryptography/README.md) / [Identity](../Identity/README.md) reading, not production hunting.

### Purple add-on

For one XSS and one SQLi success, write:

- What the access log line looked like  
- One WAF / prepared-statement / CSP-style control that would block it  
- Which Defensive-Ops telemetry class would store the evidence  

## Next

- [Wargames and CTF fundamentals](./10_Wargames_And_CTF_Fundamentals.md)

## Further reading

- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [Juice Shop docs](https://pwning.owasp-juice.shop/)
- [OWASP WebGoat](https://owasp.org/www-project-webgoat/)
- [DVWA](https://github.com/digininja/DVWA)
- [OWASP WrongSecrets](https://owasp.org/www-project-wrongsecrets/)
