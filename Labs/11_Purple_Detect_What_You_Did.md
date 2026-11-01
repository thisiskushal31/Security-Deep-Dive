# Purple: detect what you did

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Purple practice closes the loop: after an authorized attack path, you **hunt your own footprints** and write the detection or control that should catch it next time. Program depth: [Defensive-Ops](../Defensive-Ops/README.md) (exercises, detection engineering, metrics).

## Basic apply

### The minimum purple note (every offensive lab)

After any successful lab exploit, add three lines:

```markdown
## Purple
- Evidence left behind: <log line / process / file / auth event>
- How I would detect it: <rule idea in plain language>
- How I would reduce it: <patch / config / MFA / segmentation>
```

No SIEM required for the habit — imagination first, tooling second.

### One-hour purple session

```text
Authorized target ──▶ Run ONE technique you already know
                              │
                              ▼
                    List artifacts on victim / proxy / IdP lab
                              │
                              ▼
                    Write detection idea ──▶ Link Defensive-Ops note
```

Examples of “one technique”: SSH brute lab against local DVWA login (rate-limit lesson), Juice Shop XSS reflected in access logs, Nmap scan visible in firewall logs on a lab VM you own.

### Map ATT&CK lightly

| You did | Write |
|---------|-------|
| Technique name in your words | Optional ATT&CK ID if you know it |
| Data source that should see it | e.g. process creation, web logs, auth logs |

## Advanced apply

### Full purple lab day

1. **Red:** Complete an easy box or Juice Shop challenge set; keep a step list.  
2. **Blue:** Without looking at the step list, reconstruct what happened from logs/pcap/memory available in that lab.  
3. **Compare:** Diff your reconstruction vs the real steps; note blind spots.  
4. **Ship:** One detection or hardening change in *lab* config (Wazuh rule, Sysmon, WAF lab, …).  
5. **Retest:** Replay the technique; confirm the signal fires.

### Pair estates

| Offense estate | Defense estate |
|----------------|----------------|
| GOAD / HTB AD lab | DetectionLab / SIEM lab ([7](./7_Blue_SOC_And_DFIR_Labs.md), [8](./8_AD_And_Windows_Estates.md)) |
| CloudGoat scenario | CloudTrail / equivalent in the same lab account ([9](./9_Cloud_Vulnerable_By_Design.md)) |
| Local web app | Reverse-proxy + app logs + Burp history as “PCAP-lite” |

### Metrics that matter for *you*

| Metric | Use |
|--------|-----|
| % of offensive notes with a purple section | Habit health |
| Detections that fired on retest | Signal quality |
| Time to reconstruct timeline | IR skill |

Skip vanity “100 rooms” counts if purple coverage is near zero.

## Next

- [Path map](./12_Path_Map.md)

## Further reading

- [Defensive-Ops](../Defensive-Ops/README.md)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [NIST — Cyber Ranges](https://www.nist.gov/system/files/documents/2018/02/13/cyber_ranges.pdf)
