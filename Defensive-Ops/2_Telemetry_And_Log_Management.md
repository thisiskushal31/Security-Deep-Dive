# Telemetry and log management

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Detections are only as strong as the **records you kept**. Log management covers generation, transmission, storage, analysis, and disposal of computer security logs — before you argue about SIEM (Security Information and Event Management) brands. Platform notes: [3](./3_SIEM_And_Detection_Platforms.md).

## Concept

### Why logs fail quietly

| Failure | Result |
|---------|--------|
| Never generated | Blind to the action |
| Generated on host, never shipped | Lost when host is wiped |
| Shipped, never parsed | Fields you cannot query |
| Retained too short | Forensics and compliance miss the window |
| World-readable or attacker-writable | Trust collapsed |

```text
Generate ──▶ Protect in transit ──▶ Store (integrity + access)
                                      │
                                      ├──▶ Detect / hunt / IR
                                      └──▶ Dispose per policy
```

### Minimum security-relevant sources

| Source | Typical questions answered |
|--------|----------------------------|
| Identity / SSO (single sign-on) / IdP (Identity Provider) | Who authenticated, from where, MFA (multi-factor authentication) outcome |
| Endpoint / EDR (Endpoint Detection and Response) | Process, file, persistence |
| Cloud control-plane audit | Who changed IAM (Identity and Access Management), storage, network |
| Email / gateway | Phish delivery, spoof attempts |
| Network / DNS / proxy / firewall | Where traffic went; blocked vs allowed |
| Critical apps / API gateways | AuthZ failures, admin actions |
| Privileged access / PAM (Privileged Access Management) | Break-glass use |

Map sources to ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) **data components** when you plan coverage ([4](./4_Detection_Engineering_And_ATTCK.md)).

### Retention is a decision, not a default

| Driver | Effect |
|--------|--------|
| Investigation needs | Keep enough history to rebuild timelines |
| Legal / regulatory holds | Freeze and extend when required |
| Cost | Tier hot vs cold storage deliberately |
| Privacy | Minimize personal data; purpose-limit |

## Advanced

### Integrity and access

| Habit | Why |
|-------|-----|
| Separate logging credentials from admin roles | Compromised admin should not silently erase their trail |
| Time sync (NTP (Network Time Protocol) / chrony) | Timelines collapse without synchronized clocks |
| Hash / WORM (write once read many) / immutability options | Tamper resistance for high-value logs |
| Least privilege on search UIs | SOC (Security Operations Center) analysts ≠ full cloud admins |

### Volume without value

Collecting “everything” without use cases burns budget and hides signal. Prefer:

1. Define detections / IR questions first.  
2. Enable the sources those questions need.  
3. Add volume only when a gap is proven (missed purple test, missed incident).

### Network captures vs logs

Full packet capture answers different questions than syslog. Use PCAPs (packet captures) selectively; install and first use stay in [Tooling Wireshark / tcpdump](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive). Protocol/metadata logs (Zeek-class) often give better long-term hunt value than keeping every packet — [Zeek](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Zeek). Wire theory: [Networks-Deep-Dive](https://github.com/thisiskushal31/Networks-Deep-Dive).

### Privacy and minimization

Security logs can contain personal data. Purpose-limit fields, restrict search roles, and align retention with privacy policy. Breach clocks still need enough history to investigate — coordinate with counsel when designing defaults ([14](./14_Comms_Notification_And_External_Reporting.md)).

### Deception telemetry (optional control)

Canary tokens, honeypot accounts, and decoy file shares produce **high-fidelity** alerts when touched. They are not a SIEM replacement; they are tripwires. Place only where false triggers are rare and response is defined.

## Apply

1. List ten log sources you already have; mark which reach a searchable store.  
2. For one critical app, write three IR questions and the fields required to answer them.  
3. Check clock sync on two sample hosts and your log collector.  
4. Document retention for identity and cloud audit logs.  
5. Mark one high-noise source to sample or drop until a use case exists.

## Further reading

- [NIST SP 800-92 — Guide to Computer Security Log Management](https://csrc.nist.gov/pubs/sp/800/92/final)
- [MITRE ATT&CK — Data Sources](https://attack.mitre.org/datasources/)
- [CISA — Cybersecurity Performance Goals](https://www.cisa.gov/cross-sector-cybersecurity-performance-goals)
