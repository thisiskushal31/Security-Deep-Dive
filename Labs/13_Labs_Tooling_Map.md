# Labs tooling map

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Labs choose **targets**; Tooling owns **installs**. One product → one Tooling folder. This map is only “what to install before which lab type.”

Primary homes: [Tooling Security](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security).

## Basic apply

### Minimum kits by lab type

| Lab type | Install first | Tooling path |
|----------|---------------|--------------|
| Any remote platform | VPN / browser lab as platform provides | — |
| Network recon rooms | Nmap | [Reconnaissance/Nmap](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Reconnaissance/Nmap) |
| Web Academy + Juice Shop | Burp or ZAP | Offensive / DevOps ZAP as applicable |
| Packet challenges | Wireshark | [Defensive/Wireshark](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wireshark) |
| Content discovery labs | ffuf or Gobuster | Reconnaissance |
| Password labs (authorized) | John / Hashcat | Offensive |

```text
Lab note says “need X”
        │
        ▼
Tooling Security / <folder> ──▶ install + first use
        │
        ▼
Return to Labs path — do not fork a second install guide here
```

### Do not duplicate

| Need | Wrong | Right |
|------|-------|-------|
| IdP for app login testing | New IdP tree under Labs | [Tooling Identity](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Identity) (Keycloak / authentik) |
| SIEM for purple | SIEM docs inside Labs | [Wazuh](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wazuh) |
| AD attack tools | Copy Impacket README into Labs | [Offensive](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Offensive) |

## Advanced apply

### Role kits

| Track | Add when graduating |
|-------|---------------------|
| Pentester | Impacket, BloodHound, NetExec, Burp depth |
| Blue | Volatility, Autopsy, osquery, Velociraptor, Suricata/Zeek |
| Cloud | Cloud CLI in lab profiles only; never mix prod keys |
| RE / pwn CTF | Ghidra / radare2 / Cutter from RE tooling |
| Identity-heavy | Keycloak lab + oauth2-proxy against local apps |

### Lab + tool session template

1. Open the Labs note for the target type.  
2. Install missing tools from Tooling (one folder).  
3. Run the basic-apply drill.  
4. Record tool version + command *intent* in personal notes (not a command encyclopedia in this repo).  
5. Purple section if offense ([11](./11_Purple_Detect_What_You_Did.md)).

### Capability alternatives

Prefer the Tooling README “by capability” tables (Gobuster *or* ffuf *or* feroxbuster). Labs should not invent a third content-discovery home.

## Next

- [Security engineer lab tracks](./14_Security_Engineer_Lab_Tracks.md)

## Further reading

- [Tooling Security README](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security)
- [Path map](./12_Path_Map.md)
- [Identity tooling map](../Identity/16_Identity_Tooling_Map.md) (when labs need IdPs)
