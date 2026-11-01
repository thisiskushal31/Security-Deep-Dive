# Digital forensics for IR

[← Defensive-Ops](./README.md)

> **Progression:** concept → advanced → apply.

Digital forensics answers **what happened on a system** using preserved evidence so IR (incident response) can contain and eradicate with facts. This note is for engineers and responders — not a law-enforcement certification track. Integrating forensic techniques into IR is a process: collect, examine, analyze, report — with chain-of-custody discipline when stakes require it.

## Concept

### Goals inside an incident

| Goal | Practice |
|------|----------|
| Preserve | Snapshot disk/memory; keep custody notes when needed |
| Analyze | Timeline from logs, files, memory, network |
| Attribute carefully | Indicators and evidence — not courtroom claims without process |
| Support IR | Feed containment and eradication ([8](./8_Contain_Eradicate_Recover.md)) |

```text
Live incident
     │
     ├─▶ volatile capture (if skilled + authorized)
     ├─▶ disk / VM snapshot
     ├─▶ export EDR (Endpoint Detection and Response) / cloud / IdP (Identity Provider) logs
     │
     ▼
 examine → analyze → findings into IR decisions
```

### Common evidence types

| Source | Use |
|--------|-----|
| Disk / volume image | Files, persistence, deleted artifacts |
| Memory | Running malware, keys, processes |
| Packet capture | C2 (command and control), exfil paths |
| Cloud audit / IdP logs | Who did what from where |
| Endpoint telemetry | Process timelines without full disk image |
| Mobile device (when in scope) | Separate skill set and legal constraints |

Tool installs for memory and packets: [Volatility](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Volatility) · [Wireshark](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Wireshark) · [tcpdump](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/tcpdump).

## Advanced

### Order of volatility (practical)

Capture the most fragile first when skilled responders are present: memory and network state before power-off decisions. Unskilled “pull the plug every time” can destroy useful evidence *or* be correct for safety — decide with a playbook, not vibes.

### Integrity habits

| Habit | Why |
|-------|-----|
| Hash images (SHA-256 family) | Prove later copies match |
| Write-blockers / read-only mounts | Avoid altering evidence |
| Document who touched what when | Chain of custody |
| Work on copies | Keep a pristine original |

### Cloud forensics is mostly logs + snapshots

You rarely “seize a disk” in SaaS. You export audit logs, snapshot volumes you control, freeze IAM (Identity and Access Management) changes, and preserve object versions. Patterns: [10](./10_Cloud_Identity_And_SaaS_IR.md).

### Disk analysis path (lab)

For hosts you own in lab: image or use a forensic browser on a copy — [Autopsy](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Autopsy). Classify suspicious files with [YARA](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/YARA). Memory: [Volatility](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive/Volatility).

### Reporting for IR (not a novel)

| Section | Content |
|---------|---------|
| Scope | Systems, time window, data classes |
| Timeline | Facts with timestamps |
| Findings | Persistence, creds, lateral, exfil indicators |
| Recommendations | Contain/eradicate/recover actions |
| Limitations | What you could not examine |

### Engineer rules of thumb

1. Do not power-wash a box you may need to image.  
2. Prefer documented hashes and read-only analysis.  
3. Practice only on images and labs you are allowed to analyze.  
4. Legal holds, HR cases, and criminal matters escalate to counsel / specialized DFIR (digital forensics and incident response).

## Apply

1. Add “preserve before reimage” checkpoints to your malware playbook.  
2. On a lab VM, practice: snapshot → hash → inspect → document.  
3. List which cloud audit logs you can export in under 15 minutes.  
4. Know the pager path to specialized DFIR / legal for high-impact cases.  
5. Draft a one-page forensic report template for Sev-1.

## Further reading

- [NIST SP 800-86 — Guide to Integrating Forensic Techniques into Incident Response](https://csrc.nist.gov/pubs/sp/800/86/final)
- [NIST SP 800-101 Rev. 1 — Guidelines on Mobile Device Forensics](https://csrc.nist.gov/pubs/sp/800/101/r1/final)
- [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [Tooling Security / Defensive](https://github.com/thisiskushal31/Tooling-and-Frameworks-Deep-Dive/tree/main/Security/Defensive)
