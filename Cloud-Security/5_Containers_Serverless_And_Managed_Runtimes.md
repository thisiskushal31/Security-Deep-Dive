# Containers, serverless, and managed runtimes

[← Cloud-Security](./README.md)

> **Progression:** concept → advanced → apply.

Containers, serverless / **FaaS (Function as a Service)**, and managed application platforms as runtime shapes on the broader cloud surface in [4](./4_Securing_Cloud_Resources_And_Workloads.md). Cluster product depth: [Containerization-Deep-Dive](https://github.com/thisiskushal31/Containerization-Deep-Dive).

## Concept

### Where these fit

| Runtime | What changes vs a plain VM | What does *not* change |
|---------|----------------------------|-------------------------|
| **Containers / K8s** | Shared kernel, orchestration API, denser lateral movement | Still need IAM, network, secrets, image integrity, logging |
| **Serverless / FaaS** | No guest OS for you to patch; event-driven invoke | Execution identity, input trust, egress, secrets |
| **Managed app PaaS** | Provider runs runtime; you ship code/config | Authn/z, data, private networking, dependency risk |

```text
[4] Management plane, network, resources, and workloads
                         │
                         ▼
[5] Containers / Kubernetes · FaaS · managed application platforms
```

### Containers and Kubernetes (program view — not the whole cloud)

Use a shared risk catalog when you *do* run clusters:

| ID | Risk | First response |
|----|------|----------------|
| **K01** | Insecure workload configs | Non-root; drop caps; no privileged; read-only root FS |
| **K02** | Overly permissive authorization | Least-privilege RBAC (Role-Based Access Control); avoid `cluster-admin` / chart wildcards |
| **K03** | Secrets management failures | No secrets in images/Git; external store; short-lived tokens |
| **K04** | No cluster policy enforcement | PSS / admission as gates |
| **K05** | Missing network segmentation | NetworkPolicy where trust needs it |
| **K06–K07** | Exposed / vulnerable control plane | Private API; patch; CIS-class hardening |
| **K08** | Cluster → cloud lateral movement | Per-workload cloud IAM; block node metadata abuse |
| **K09–K10** | Broken authn / weak logging | Strong API authn; audit + alert |

**PSS (Pod Security Standards):** Privileged (unrestricted) → Baseline (block known escalations) → Restricted (hardening defaults). Enforce; do not wiki.

```yaml
# Illustrative workload hardening — not a full cluster guide
securityContext:
  runAsNonRoot: true
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: true
  capabilities: { drop: ["ALL"] }
  seccompProfile: { type: RuntimeDefault }
```

On managed Kubernetes, prefer **ServiceAccount → cloud role** over one fat node role; deny pod egress to link-local metadata where possible. Deep PSS/RBAC/etcd: Containerization-Deep-Dive + [OWASP Kubernetes Top Ten](https://kubernetes-top10.owasp.org/).

### Serverless / FaaS

| Control | Practice |
|---------|----------|
| Execution role | One role per function (or tight family) |
| Triggers | Authenticate event sources; distrust public HTTP by default |
| Input validation | Events are attacker-controlled data |
| Secrets | Platform secret store / short-lived tokens |
| Egress | Constrain outbound when the platform allows |
| Dependencies | Minimal package; SCA ([6](./6_Supply_Chain_And_Delivery_Integrity.md)) |

### Managed app platforms (PaaS)

| Control | Ask |
|---------|-----|
| Runtime / language stack | Supported versions; who patches? |
| Network | Private inbound; outbound allow lists |
| Identity | App identity to data stores — not embedded passwords |
| Slots / environments | Prod secrets never in shared non-prod |
| Extensions / marketplace | Same supply-chain skepticism as libraries |

## Advanced

### Runtime choice does not remove resource risk

A function that writes to a public bucket is an object-storage incident. A pod that mounts a privileged host path is a compute/node incident. Triage under the owning surface in [4](./4_Securing_Cloud_Resources_And_Workloads.md) and posture in [3](./3_CSPM_And_Posture.md).

### Fitting runtimes into the wider program

| Estate emphasis | Weight |
|-----------------|--------|
| Networks, VMs, storage, data services | Notes [1](./1_Cloud_Models_And_Shared_Responsibility.md)–[4](./4_Securing_Cloud_Resources_And_Workloads.md) |
| Clusters among many accounts | KSPM as one posture slice among others |
| Serverless-heavy | Execution roles, event auth, dependency SCA |
| Private-cloud VMs | Management plane, images, network zoning |

## Apply

1. Classify each app’s runtime (VM, container, function, PaaS).  
2. Apply [4](./4_Securing_Cloud_Resources_And_Workloads.md) controls to every store, network path, and identity it touches.  
3. Add runtime-specific gates (PSS/admission, function-role lint, PaaS private endpoint).  
4. Do not let a K8s checklist substitute for org-wide public-storage and IAM posture.  

### Self-check

- [ ] Explain cloud security without mentioning Kubernetes  
- [ ] Place containers as one runtime on the resource plane  
- [ ] Name K01–K10 only as *cluster* prioritization, not the cloud program  
- [ ] List three FaaS controls that are identity/event — not OS patch  

## Next

- [Supply chain and delivery integrity](./6_Supply_Chain_And_Delivery_Integrity.md)

## Further reading

- [OWASP Kubernetes Top Ten](https://kubernetes-top10.owasp.org/)
- [Kubernetes Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)
- [NIST SP 800-190 — Application Container Security Guide](https://csrc.nist.gov/pubs/sp/800/190/final)
- [OWASP Serverless Top 10](https://owasp.org/www-project-serverless-top-10/)
- [CSA Security Guidance — Cloud Workload Security](https://cloudsecurityalliance.org/research/guidance/)
- [NSA/CISA Kubernetes Hardening Guidance](https://media.defense.gov/2022/Aug/29/2003066362/-1/-1/0/CTR_KUBERNETES_HARDENING_GUIDANCE_1.2_20220829.PDF)
- [Containerization-Deep-Dive](https://github.com/thisiskushal31/Containerization-Deep-Dive)
