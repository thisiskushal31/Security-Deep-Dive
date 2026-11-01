# AD and Windows estates

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Active Directory labs teach enumeration, auth abuse paths, and lateral movement **inside intentionally vulnerable domains**. They are resource-heavy and unsafe if exposed. Complete single-host Linux/Windows boxes first ([5](./5_Unguided_Boxes_And_Pro_Labs.md)).

## Basic apply

### Choose an estate that matches your hardware

| Option | Basic apply |
|--------|-------------|
| **GOAD** (and light variants) | Follow upstream docs; deploy MINILAB / light before full multi-domain |
| **HTB AD / Pro Lab tracks** | Platform-hosted — less local RAM, still authorized-only |
| **DetectionLab** | Defender logging focus — [7](./7_Blue_SOC_And_DFIR_Labs.md) |

```text
Isolated hypervisor network
    ├── Domain Controller(s)
    ├── Member servers / workstations
    └── Your attacker VM (tools from Tooling Offensive — lab only)
```

### First AD session goals (not “domain admin or bust”)

1. Document domain name, DCs, trust sketch.  
2. Enumerate users/groups with **lab-allowed** tools (Impacket/BloodHound installs in Tooling).  
3. Find one misconfiguration path; exploit only if in scope of that lab’s design.  
4. Snapshot before destructive actions.  
5. Notes: path graph in prose + which Defensive signal should have fired.

### Non-negotiable hygiene

| Rule | Why |
|------|-----|
| No internet exposure of RDP/SMB/LDAP | Instant compromise magnet |
| No prod creds / real user sync | Contaminates identity and legal scope |
| Tear down when idle if on cloud hypervisors | Cost + risk |

## Advanced apply

### Attack path literacy checklist

Practice until you can explain (in notes) without spoilers open:

| Theme | You can demonstrate in-lab |
|-------|----------------------------|
| Kerberos basics | TGT/TGS concepts in your writeup |
| Credential reuse / spraying limits | Controlled lab policy only |
| Delegation / ACL abuse classes | One path documented end-to-end |
| Lateral movement | Explicit hop list with credentials used |

### Purple AD

1. Perform a known technique in GOAD-class lab.  
2. On DetectionLab (or your SIEM-backed lab), locate Sysmon / security events.  
3. Write a detection with expected noise.  
4. Link Defensive-Ops detection + IR notes.

### Escalation ladder

```text
Single Windows box privesc ──▶ Small AD (1 DC + 1 client)
                                      │
                                      ▼
                         Multi-domain / forest lab ──▶ Pro Lab networks
```

Skip rungs and you will thrash BloodHound without understanding outputs.

## Next

- [Cloud vulnerable-by-design](./9_Cloud_Vulnerable_By_Design.md)

## Further reading

- [GOAD](https://github.com/Orange-Cyberdefense/GOAD) · [GOAD docs](https://orange-cyberdefense.github.io/GOAD/)
- [DetectionLab](https://github.com/clong/DetectionLab)
- [HTB Pro Labs](https://www.hackthebox.com/hacker/pro-labs)
- [Tooling Offensive](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Offensive)
