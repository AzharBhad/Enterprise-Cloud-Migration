# 28. Compliance Considerations

> **Part D: Risk, Compliance, Governance** · Section 28 of 33 · Lead: Security Team + Compliance/Privacy Officer · **[ASSUMPTION ASM-01]** healthcare (HIPAA/HITECH, HITRUST r2, PCI DSS v4.0.1, 21 CFR Part 11)
>
> *This section is an engineering view of compliance impact. It isn't legal advice. Validate it with Compliance, Privacy, Legal, and your assessors.*

## 28.1 Regulatory landscape and program impact

| Framework | Relevance | Program impact |
|---|---|---|
| **HIPAA Security Rule** (45 CFR §164.308–316) | All ePHI on endpoints, M365, file shares | Risk analysis update for the new architecture; administrative, physical, and technical safeguards mapped to cloud controls (§28.2) |
| **HIPAA Privacy & Breach Notification Rules** | Data migration, oversharing, lost devices | Breach risk assessment process for migration incidents; encryption safe harbor (BitLocker/FileVault + escrow) |
| **HITECH** | Breach enforcement, BA obligations | Microsoft **BAA** in place and in-scope services confirmed (PR-A7) |
| **Proposed HIPAA Security Rule update** (NPRM published Jan 2025; status to monitor) | Would make MFA, encryption, asset inventory, network segmentation, and vulnerability scanning more explicitly required | The target architecture already meets these expectations; track final rule status |
| **HITRUST CSF r2** | Certification held; recertification during the program | Evidence sources change; brief the assessor; map controls (§28.4) |
| **PCI DSS v4.0.1** | Registration/check-in payment kiosks and desks | W9 kiosk wave: segmentation, App Control, MFA for CDE admin access, change control with QSA review |
| **21 CFR Part 11** | Lab/research systems with e-records/e-signatures | Validated systems: endpoint changes (OS, join type) for validated workstations need **change control & re-validation** (IQ/OQ evidence); most go to Path D or a validated build |
| **State privacy/breach laws** | Breach notification timelines | Incident response playbook includes state requirements |
| **42 CFR Part 2** (if SUD programs exist) | Extra-sensitive records | Sensitivity label "Highly Confidential – Part 2"; DLP rule |
| **Information blocking (21st Century Cures Act)** | EHR access | Not materially affected (EHR out of scope) |
| **Joint Commission (IM standards)** | Information management, downtime procedures | Downtime procedures and continuity documentation (§22) |
| **Cyber insurance requirements** | MFA, EDR, backups, privileged access | Target controls usually *improve* insurability; share evidence at renewal |

## 28.2 HIPAA Security Rule: safeguard mapping

| HIPAA standard | Implementation specification | Target control(s) | Evidence source |
|---|---|---|---|
| §164.308(a)(1) Security management | Risk analysis, risk management, sanction policy, info system activity review | Updated risk analysis covering Entra/Intune/M365/Azure; Sentinel activity review | Risk analysis doc; Sentinel workbooks |
| §164.308(a)(3) Workforce security | Authorization/supervision, clearance, termination | Lifecycle Workflows **[E5/Governance]**, HR-driven provisioning, access reviews | Entra audit logs, access review history |
| §164.308(a)(4) Information access management | Access authorization, establishment/modification | RBAC, PIM **[E5]**, group-based SharePoint permissions | PIM reports, SPO permission reports |
| §164.308(a)(5) Security awareness & training | Protection from malicious software, log-in monitoring, password management | MDE, Defender for Office, attack simulation training **[E5]**, WHfB/passwordless | MDE reports, training completion |
| §164.308(a)(6) Security incident procedures | Response and reporting | Defender XDR + Sentinel incidents, playbooks | Incident records |
| §164.308(a)(7) Contingency plan | Data backup, DR, emergency mode, testing | OneDrive/SPO retention + M365 Backup **[ADD-ON]**, Autopilot rebuild, DR tests (§22.6) | DR test reports |
| §164.308(a)(8) Evaluation | Periodic technical evaluation | Secure Score, Intune compliance, pen tests (M9, M17) | Reports |
| §164.308(b) Business associate contracts | BAAs | Microsoft BAA; partner BAAs (packaging/migration partners with PHI access) | Contracts |
| §164.310(a) Facility access | — | Out of program scope (physical) | — |
| §164.310(b)(c) Workstation use & security | Policies, physical safeguards | Intune compliance, screen lock, shared-device timeouts, kiosk lockdown | Intune policy reports |
| §164.310(d) Device & media controls | Disposal, media reuse, accountability, backup | Autopilot deregistration + wipe on disposal, BitLocker, Device Control (USB), asset inventory (Intune + MDE) | Disposal records, Intune inventory |
| §164.312(a) Access control | Unique user ID, emergency access, automatic logoff, encryption | Unique Entra IDs (generic accounts removed), break-glass, auto-logoff policies, BitLocker/FileVault | Entra, Intune encryption report |
| §164.312(b) Audit controls | Record and examine activity | Purview Audit (Premium **[E5]**), Sentinel, Intune audit logs | Log retention config |
| §164.312(c) Integrity | Mechanism to authenticate ePHI | Versioning, retention labels, immutable archive, MDE tamper protection | Config |
| §164.312(d) Person or entity authentication | — | Phishing-resistant MFA, CA authentication strengths, CBA | Sign-in logs |
| §164.312(e) Transmission security | Integrity controls, encryption | TLS everywhere, SMB encryption for Azure Files, Private Access, S/MIME / Purview Message Encryption | Config |

## 28.3 Compliance-sensitive design decisions

| Decision | Compliance consideration | Position |
|---|---|---|
| Generic/shared accounts on clinical workstations | HIPAA unique user identification (§164.312(a)(2)(i)) | Eliminate generic **user** accounts for PHI access; badge-based individual sign-in; kiosks use device identities with no PHI access |
| Endpoint Analytics / Remote Help / Advanced Analytics | Workforce monitoring perception, privacy | Publish a privacy notice; RBAC; Remote Help requires user consent (except for elevated policies per RBAC); retain logs per policy |
| BYOD MAM | Personal device privacy vs. PHI protection | MAM only (no device management); selective wipe; privacy statement in Company Portal |
| Log retention | HIPAA documentation retention (6 years for policies/procedures); audit log retention per risk analysis | Sentinel: 90 days interactive + long-term retention in the data lake/archive tier per policy; Purview Audit Premium for 1-year+ **[E5]** |
| Data residency | Contractual/state requirements | US tenant data location; Azure regions US; Multi-Geo not required |
| Copilot / AI features in admin tools (Security Copilot, Copilot in Intune) | PHI in prompts, BAA coverage | Confirm BAA scope before enabling; RBAC; prompt guidance |
| Archive/delete (D-05) | Retention schedules, legal holds | Legal sign-off; immutable archive with retention policy |
| Medical devices | FDA-cleared configurations | No Intune management; only identity/network dependency remediation with vendor approval |
| Part 11 validated workstations | Validation status | Path D or validated Autopilot build with documented IQ/OQ; change control |

## 28.4 HITRUST and assessor engagement

| Step | Timing | Owner |
|---|---|---|
| Brief the external assessor on the architecture transition and evidence-source changes | M2 | Compliance |
| Control-to-evidence mapping update (old: GPO/MECM/AD FS reports → new: Intune/Entra/Sentinel/Purview exports) | M3–M6 | SE |
| Decide assessment scope timing (avoid fieldwork during the peak transition M9–M13 if possible) | M3 | Compliance + ES |
| Automated evidence: scheduled Graph exports of compliance policies, CA policies, device compliance, encryption status into an evidence library (SharePoint, retention label) | M6 | EP + SE |
| Interim assessment / readiness check | M12 | Compliance |

## 28.5 Compliance tooling

| Tool | Use | Licensing |
|---|---|---|
| **Microsoft Purview Compliance Manager** | HIPAA/HITECH and HITRUST assessment templates, improvement actions, evidence upload | Premium templates **[E5]** / add-on |
| **Microsoft Defender for Cloud** regulatory compliance dashboard | Azure landing zone HIPAA/HITRUST initiative | Defender for Cloud plans **[ADD-ON]** |
| Intune compliance reports + Graph exports | Endpoint evidence | E3 |
| Entra access reviews, PIM audit history | Access governance evidence | **[E5/P2]** |
| Sentinel workbooks | Activity review evidence | Consumption |

## 28.6 RACI: Compliance

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| HIPAA risk analysis update | **A** | C | R | C | C | R | C | I |
| BAA verification (Microsoft, partners) | **A** | R | C | I | I | C | I | I |
| Control-to-evidence mapping | I | I | C | R | R | **A/R** | I | I |
| HITRUST assessor engagement | C | C | C | I | I | **A** (with Compliance Officer) | I | I |
| PCI kiosk scope | I | C | C | I | R | **A** | C | I |
| Part 11 validated workstation change control | I | C | C | I | R | C | **A** | I |
| Privacy notices (monitoring, BYOD) | **A** | R | I | I | C | R | I | C |
