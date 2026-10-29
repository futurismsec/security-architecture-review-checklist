

# Security Architecture Review Checklist: 40 Questions to Ask Before You Go to Production

Most breaches don't start with an exotic zero-day. They start with a design decision made months earlier: a flat network, an over-privileged service account, a log source nobody wired into the SIEM, a "temporary" exception that became permanent.

A **security architecture review** is how you catch those decisions while they are still cheap to change. Fixing a trust boundary on a whiteboard takes an afternoon. Fixing it after go-live takes a quarter, a change board, and usually an incident.

This post gives you **40 questions** to ask before any system, migration, or major release reaches production. They are grouped into eight domains so you can run them as a working session with architects, engineering, and security in the same room.

> **Want the printable version?** Grab the downloadable checklist (PDF/Excel) from [Futurism Security](https://www.futurismsecurity.com/services/security-architecture-review/) and use it as a sign-off sheet for your next launch review.

---

## How to use this checklist

- **Run it early, then again before go-live.** The first pass shapes the design. The second confirms what was actually built.
- **Score each question** as ✅ Met, ⚠️ Partially met, ❌ Not met, or ➖ Not applicable.
- **Every ❌ needs an owner and a date.** Every ⚠️ needs a documented risk acceptance or a remediation plan.
- **Treat "I don't know" as ❌.** If nobody can answer a question, the control probably isn't there.

---

## 1. Scope, assets, and threat model

You can't protect what you haven't described. Start here.

1. **Is there a current architecture diagram** showing all components, data flows, trust boundaries, and external dependencies?
2. **Have you identified your crown-jewel assets** (data, systems, keys, credentials) and ranked them by business impact?
3. **Has a threat model been completed** (for example STRIDE or attack trees) and reviewed by someone outside the build team?
4. **Are the likely adversaries defined**: external attackers, malicious insiders, compromised third parties, ransomware operators?
5. **Are all assumptions written down**, such as "the internal network is trusted" or "this API is only called by service X"? Unstated assumptions are where vulnerabilities live.

## 2. Identity and access management

Identity is the new perimeter. Most modern attacks end up abusing it.

6. **Is least privilege enforced** for users, service accounts, and CI/CD pipelines, with no wildcard permissions or shared admin roles?
7. **Is MFA required** for all human access, with phishing-resistant methods (FIDO2/passkeys) for privileged users?
8. **Are privileged accounts managed** through just-in-time access, session recording, and a vault, rather than standing admin rights?
9. **How are machine identities and secrets handled?** Are API keys, certificates, and tokens stored in a secrets manager, rotated automatically, and kept out of code and images?
10. **Is there a joiner/mover/leaver process** that removes access promptly, and a periodic access recertification that actually gets done?

## 3. Network and segmentation

Assume something inside will eventually be compromised. Design so that it can't go far.

11. **Is the network segmented** by environment, sensitivity, and function, with production isolated from dev and test?
12. **Is east-west traffic controlled**, using microsegmentation or equivalent so lateral movement requires explicit allow rules?
13. **Is ingress and egress filtered**, with default-deny egress and monitoring for unexpected outbound connections?
14. **Are management planes protected**: admin interfaces, bastions, and consoles reachable only through hardened, monitored paths, never directly from the internet?
15. **Is there DDoS and WAF protection** in front of internet-facing services, tuned for your actual traffic and not left in log-only mode?

## 4. Data protection

Know where the data is, who touches it, and what happens if it leaks.

16. **Is data classified**, and do controls scale with classification?
17. **Is data encrypted in transit and at rest**, with modern protocols and no legacy fallbacks such as TLS 1.0/1.1?
18. **Who owns the encryption keys?** Are keys managed in a KMS or HSM, separated from the data they protect, with rotation and access logging?
19. **Is sensitive data minimized, masked, or tokenized** in non-production environments and analytics pipelines?
20. **Are backups protected**: encrypted, immutable or offline copy available, access-restricted, and restore-tested?

## 5. Cloud, platform, and infrastructure

Misconfiguration remains the leading cause of cloud exposure.

21. **Are landing zones and guardrails in place** (policies, SCPs, org-level controls) so insecure resources can't be deployed in the first place?
22. **Is infrastructure defined as code**, scanned for misconfigurations before deployment, and prevented from drifting afterward?
23. **Are storage, databases, and snapshots verified as non-public**, with continuous posture monitoring (CSPM) catching regressions?
24. **Are workloads hardened**: minimal base images, patched hosts, no unnecessary open ports, and runtime protection for containers and VMs?
25. **For hybrid, OT, or IoT components, are they isolated and monitored**, given that they often can't be patched or agent-instrumented?

## 6. Application and supply chain security

Your system is only as strong as the code and components it runs.

26. **Is security built into the SDLC**, with SAST, DAST, dependency scanning, and secret scanning running in the pipeline and gating releases on critical findings?
27. **Do you maintain an SBOM** and track vulnerable third-party libraries and base images?
28. **Is the build pipeline itself secured**: signed artifacts, protected branches, isolated runners, and no long-lived credentials in CI?
29. **Are APIs protected** with strong authentication, authorization checks at the object level, input validation, and rate limiting?
30. **Have third-party and SaaS integrations been reviewed** for the data they receive, the permissions they hold, and what happens if they are breached?

## 7. Logging, detection, and response

If you can't see it, you can't stop it, and you can't investigate it afterward.

31. **Are the right logs collected** (identity, network, cloud control plane, application, endpoint) and sent to a central platform?
32. **Are logs tamper-resistant and retained** long enough for investigations and compliance, typically well beyond 90 days for critical sources?
33. **Do detections exist for this system's specific threats**, mapped to your threat model and tested, not just generic default rules?
34. **Are the SIEM, SOAR, EDR/XDR, IAM, and CASB integrated** so an alert can trigger context enrichment and automated containment?
35. **Is there an incident response plan and runbook** for this system, with named owners, escalation paths, and a tested tabletop in the last 12 months?

## 8. Resilience, governance, and compliance

Security that can't survive a bad day, or an audit, isn't finished.

36. **Are RTO and RPO defined and proven** through actual failover and restore tests rather than assumed from vendor claims?
37. **Is there protection against ransomware scenarios**, including isolated recovery environments and protected backup credentials?
38. **Are applicable regulations mapped to controls** (for example HIPAA, PCI DSS, GDPR, SOX, NIST, ISO 27001, DPDP) with evidence collection that doesn't depend on heroics?
39. **Are exceptions and risk acceptances documented**, time-boxed, and approved by someone with the authority to accept the risk?
40. **Is there a post-launch plan**: ongoing posture reviews, a defined owner for the architecture, and a trigger for re-review when the design changes materially?

---

## Interpreting your results

| Result | What it means | What to do |
|---|---|---|
| **35-40 met** | Strong foundation | Document evidence, schedule periodic re-reviews |
| **25-34 met** | Solid but with meaningful gaps | Prioritize ❌ items tied to crown jewels and identity |
| **15-24 met** | Material risk at launch | Delay go-live or ship with compensating controls and executive sign-off |
| **Under 15 met** | Architecture is not production-ready | Pause, re-scope, and consider an independent review |

Raw counts are only a starting point. One missing control on your most sensitive data path can outweigh ten met controls elsewhere. Weight your findings by business impact.

---

## Common red flags we see in reviews

- **"We'll add security after launch."** Retrofitting is slower, costlier, and rarely finished.
- **Flat networks with strong perimeters.** One phished credential and everything is reachable.
- **Tool sprawl with no integration.** Multiple security products that don't share context or automate response.
- **Compliance treated as security.** Passing an audit proves you met a baseline on a given day, not that you can withstand an attacker.
- **No owner for the architecture.** Everyone assumes someone else is watching the whole picture.

---

## When to bring in an independent review

An internal checklist is a great start, but fresh eyes catch what familiarity hides. Consider an external security architecture review when you are:

- Migrating to or expanding in the cloud
- Launching a major digital transformation or new product
- Integrating systems after a merger or acquisition
- Preparing for a HIPAA, PCI DSS, GDPR, SOX, or NIST audit
- Adopting Zero Trust
- Securing OT, IoT, or ICS environments

[Futurism Security's Security Architecture Review](https://www.futurismsecurity.com/services/security-architecture-review/) covers current-state assessment, gap analysis, resilience and scalability review, risk prioritization through threat modeling, control optimization aligned to Zero Trust, NIST, and ISO, integration review across SIEM, SOAR, IAM, CASB, EDR, and XDR, and a future-ready roadmap.

---

## Get the downloadable checklist

Turn these 40 questions into a working sign-off sheet your team can score, assign, and track.

**[Download the Security Architecture Review Checklist](https://www.futurismsecurity.com/services/security-architecture-review/)**

Prefer to talk it through with an expert? [Book a free consultation](https://www.futurismsecurity.com/contact-us/).

---

*Published by [Futurism Security](https://www.futurismsecurity.com/). Use this checklist freely inside your organization; link back if you share it.*
