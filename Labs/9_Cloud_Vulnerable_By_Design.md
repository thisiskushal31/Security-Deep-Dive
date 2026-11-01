# Cloud vulnerable-by-design

[← Labs](./README.md)

> **Progression:** basic apply → advanced apply.

Cloud labs deploy **intentionally weak** IAM, storage, and service configurations into an account **you** control so you can practice attack paths without touching employer or customer tenants. Pair concepts with [Cloud-Security](../Cloud-Security/README.md).

## Basic apply

### Dedicated account or nothing

| Requirement | Why |
|-------------|-----|
| Separate cloud account / subscription | Blast radius; billing clarity |
| Budget alerts + hard caps | Prevent surprise spend |
| No org SSO to prod IdP | Keeps [Identity](../Identity/README.md) blast away from real users |
| Region chosen deliberately | Cost + latency for your lab |

```text
Your laptop ──▶ CloudGoat / similar CLI ──▶ Throwaway cloud account
                                              │
                                              ▼
                                    Scenario resources (vuln by design)
                                              │
                                              ▼
                                    destroy / teardown when done
```

### CloudGoat-style first scenario

1. Read the project’s install + scenario list on the official repo.  
2. Configure credentials for the **lab account only**.  
3. Create one beginner scenario; complete its objectives.  
4. Notes: which IAM / service misconfig enabled each step.  
5. Destroy the scenario before the next one.

Never point CloudGoat at a shared company account “just for a minute.”

### Map to IR

After one successful path, list:

- Which cloud audit logs showed the activity  
- What you would revoke first (keys, roles, sessions) — [Defensive-Ops/10](../Defensive-Ops/10_Cloud_Identity_And_SaaS_IR.md)

## Advanced apply

### Multi-scenario curriculum

| Order | Focus |
|-------|-------|
| 1 | IAM / credential footholds |
| 2 | Storage exposure paths |
| 3 | Compute / SSRF-shaped cloud weaknesses (as offered by scenarios) |
| 4 | Cross-service pivots |

Re-read Cloud-Security posture notes between scenarios so the lab is not only “flag hunting.”

### Purple cloud

1. Enable detailed trail/logging in the lab account before the attack.  
2. Execute the scenario.  
3. Reconstruct the timeline from logs only (second sitting).  
4. Write one detection or config guardrail you would enable in a real tenant *design* — without applying it to unauthorized tenants.

### Cleanup discipline

| Checkpoint | Action |
|------------|--------|
| End of evening | `destroy` / teardown + verify console empty |
| Weekly | Billing review for orphaned resources |
| Credential hygiene | Rotate lab access keys; never reuse in prod CLI profiles |

## Next

- [Purple: detect what you did](./11_Purple_Detect_What_You_Did.md)

## Further reading

- [CloudGoat](https://github.com/RhinoSecurityLabs/cloudgoat)
- [Cloud-Security](../Cloud-Security/README.md)
- [Defensive-Ops — Cloud, identity, and SaaS IR](../Defensive-Ops/10_Cloud_Identity_And_SaaS_IR.md)
