# Forensics

[← Defensive-Ops](./README.md)

**Digital forensics** answers what happened on a system using preserved evidence. This is literacy for engineers and IR — not a law-enforcement curriculum (NICE Investigation depth is deeper than this folder).

## Goals

| Goal | Practice |
|------|----------|
| Preserve | Snapshot disk/memory; keep chain-of-custody notes |
| Analyze | Timeline from logs, files, memory, network |
| Attribute (careful) | Indicators, not courtroom claims without process |
| Support IR | Feed containment and eradication |

## Common evidence types

| Source | Use |
|--------|-----|
| Disk image | Files, persistence, deleted artifacts |
| Memory | Running malware, keys, processes ([Volatility](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Volatility)) |
| Packet capture | C2, exfil ([Wireshark](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wireshark)) |
| Cloud audit / IdP logs | Who did what from where |
| Endpoint telemetry | EDR timelines |

## Engineer rules of thumb

1. Do not power-wash a box you may need to image.  
2. Prefer read-only mounts and documented hashes.  
3. Lab practice only on images you are allowed to analyze.  
4. Legal holds and HR cases escalate to counsel / specialized DFIR.

## Related home

- Networks blue team → Networks `Security/`  
- Tool install → Tooling `Security/Defensive/`  
- IR process → [2 Incident response](./2_Incident_Response.md)
