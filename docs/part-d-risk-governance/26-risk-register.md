# 26. Risk Register

> **Part D: Risk, Compliance, Governance** · Section 26 of 33 · Owner: Program Manager (register) · Risk owners named per row · Deep-dive analysis is in the [Risk Analysis](../risk-analysis/) section

## 26.1 Scoring model

| Dimension | Scale |
|---|---|
| **Probability** | **H** (> 50 %), **M** (20–50 %), **L** (< 20 %) during the program |
| **Impact** | Described qualitatively; scored 1–5 for severity calculation |
| **Severity** | **Critical**: patient safety, regulatory breach, tenant-wide outage, or > 3 months delay · **High**: department-wide outage, 1–3 months delay, significant cost · **Medium**: limited outage/workaround exists, < 1 month delay · **Low**: minor |

Severity matrix (Probability × Impact):

| | Impact 1–2 | Impact 3 | Impact 4 | Impact 5 |
|---|---|---|---|---|
| **H** | Medium | High | Critical | Critical |
| **M** | Low | Medium | High | Critical |
| **L** | Low | Low | Medium | High |

Risk IDs: **R-0xx** = program register (this section). **R-1xx** = hidden dependency and technical deep-dive risks (Risk Analysis section). Both feed the RAID log (App-G).

## 26.2 Platform and lifecycle risks

| ID | Risk | Impact | Prob. | Severity | Mitigation | Contingency | Owner |
|---|---|---|---|---|---|---|---|
| R-001 | Windows 10 ESU Year 1 ends 13 Oct 2026; residual Win10 devices unpatched | Unpatched PHI-handling endpoints; HIPAA risk; exploit exposure (5) | H | **Critical** | Buy ESU Year 2 in week 1 for devices not replaced by mid-Oct; accelerate Win11 in-place upgrades (capable devices) and replacements (~900 incapable) | Network-isolate remaining unpatched Win10 devices (restricted VLAN, CA block for M365), MDE in block mode | EP |
| R-002 | Hidden Kerberos/NTLM/LDAP dependencies break clinical apps after Entra join or DC changes | Clinical workflow outage (5) | H | **Critical** | §15 assessment with ≥ 60 days of telemetry; cloud Kerberos trust; Private Access; wave dependency clearance gate | Path D (keep Hybrid) for affected device groups; re-promote DC; AVD isolation | ID |
| R-003 | Tap-badge SSO vendor not certified for Entra-joined shared devices | Shared clinical devices stay Hybrid; MECM/AD retained longer (4) | M | **High** | Vendor engagement M2; lab validation M3–M4; contractual commitment; evaluate vendor's Entra-joined mode/CBA | Path D (Hybrid + Intune-only) exception with 90-day reviews; AD footprint retained for these devices | EP + AO |
| R-004 | Data migration loses permissions or overshares PHI | Privacy breach, OCR reportable (5) | M | **Critical** | Permission mapping, flatten-and-group model, sensitivity labels, pre-migration access cleanup, oversharing reports, DLP | Immediate permission lockdown (site-level), incident response, breach risk assessment per HIPAA | Data + SE |
| R-005 | Change saturation and fatigue in clinical staff | Low adoption, workarounds, safety risk (4) | H | **Critical** | Bundling, blackout calendar, super-users, at-the-elbow, clinical co-sponsor | Pause clinical waves; extend timeline for Ring 5 | Change |
| R-006 | Entra Connect Sync below the minimum version after 30 Sep 2026 | All sync fails: new hires, password changes (PHS), group changes (5) | M (unknown) | **Critical** | Day-1 verification and upgrade to current 2.6.x; staging server parity | Upgrade immediately (Microsoft states sync resumes after upgrade); manual cloud user creation for urgent hires | ID |
| R-007 | ConfigMgr 2503 at/near end of support; compliance service deprecation Oct 2026 affects co-managed compliance | No fixes; co-managed compliance checks may fail (4) | M (upgrade planned M2) | **High** | Upgrade to 2609 by M2 (before co-management enablement) | Delay co-management compliance workload until upgraded | EP |
| R-008 | Microsoft-initiated Connect Sync → Cloud Sync transition window lands mid-program | Unplanned identity work (3) | M | Medium | Monitor Message Center; readiness assessment; Connect Sync retained while Hybrid devices exist (documented blocker) | Engage Microsoft for deferral given device-sync dependency | ID |
| R-009 | Product changes (licensing packaging, feature deprecation, renames) during the program | Rework, budget variance (3) | H | High | Monthly "Microsoft change radar" review (Message Center, Intune What's New, roadmap); contract flexibility | Re-plan affected workstream; change request | AR |

## 26.3 Identity and security risks

| ID | Risk | Impact | Prob. | Severity | Mitigation | Contingency | Owner |
|---|---|---|---|---|---|---|---|
| R-010 | Conditional Access misconfiguration locks out users (incl. clinical) | Mass outage (5) | M | **Critical** | Report-only first; what-if + Maester tests; test tenant; staged by ring; break-glass | Emergency revert to report-only (< 15 min) | SE |
| R-011 | Break-glass accounts unavailable/misconfigured when needed | Prolonged tenant lockout (5) | L | High | Quarterly tests; FIDO2 keys in separate safes; excluded from CA; alerting | Microsoft support data-protection recovery process (slow) | ID |
| R-012 | Defederation causes authentication failures for apps with AD FS-specific claims | App outages (4) | M | High | RPT classification; migrate RPTs before defederation; 100 % staged rollout for 30 days | Re-federate (AD FS warm for 30 days) | ID |
| R-013 | Attacker exploits the transition (dual paths, exclusions, temporary admin rights) | Breach (5) | M | **Critical** | Exclusion governance with expiry; PIM; MDI; Sentinel detections for transitional components (AD FS, Connect, CMG) | IR retainer; isolate transitional components | SE |
| R-014 | Users can't register phishing-resistant methods (no smartphone, shared accounts) | Lockouts, weaker MFA exceptions linger (3) | M | Medium | FIDO2 keys for no-phone users; TAP; badge CBA; kiosk device identities | Time-boxed exception group with Authenticator OTP hardware tokens | ID |
| R-015 | Legacy EDR removal leaves gaps or conflicts with MDE | Detection gap (4) | L | Medium | MDE passive mode side-by-side; EDR block mode; per-ring removal | Re-enable legacy EDR in affected ring | SE |
| R-016 | EPM rules too restrictive → productivity loss / too permissive → privilege abuse | Productivity or security (3) | M | Medium | Audit-mode data; support-approved default; weekly rule review | Temporary user-confirmed rules for affected group | SE |
| R-017 | Standing privileged accounts persist because PIM not licensed (E5 not approved) | Elevated breach risk (4) | M | High | D-01 business case; minimum: P2 for admins only | Compensating: separate cloud admin accounts, CA + PAW, monitoring | ES |

## 26.4 Endpoint, application, and data risks

| ID | Risk | Impact | Prob. | Severity | Mitigation | Contingency | Owner |
|---|---|---|---|---|---|---|---|
| R-018 | Entra-joined devices can't get Wi-Fi certs / ISE can't authorize them | Devices unusable on-site (5) | M | **Critical** | §16 proven in M4 (critical path); onboarding SSID; ISE lab | Temporary PEAP/onboarding SSID with restricted access; delay pilot | ID + Network |
| R-019 | Path B wipe destroys local data not in OneDrive | Data loss, user trust (4) | M | High | KFM hard gate, local data scan, user attestation | Recovery attempts limited; spare device swap; incident review | EP |
| R-020 | App packaging backlog delays waves | Schedule slip (3) | H | High | Rationalize first; partner factory; EAM catalog; wave readiness gate | Isolate unpackaged apps on AVD; defer affected departments | EP |
| R-021 | Peripheral drivers (signature pads, scanners, label printers) fail on Win11/Entra-joined | Clinical/registration workflow outage (4) | M | High | Clinical sim lab; vendor driver validation; driver as Win32 dependency | Keep affected workstations Path D; vendor escalation | EP + AO |
| R-022 | Autopilot failure rate high (ESP timeouts, TPM attestation, network) | Delays, poor experience (3) | M | Medium | Thin ESP; pre-provisioning for clinics; Connected Cache; TPM firmware updates | Spare device swaps; technician-led provisioning | EP |
| R-023 | Bandwidth saturation at clinics during waves (Autopilot, KFM uploads, updates) | Clinical system slowness (4) | M | High | Delivery Optimization, Connected Cache, KFM upload throttling, local breakout prerequisite (PR-N2) | Re-schedule tranche to after-hours | Network + EP |
| R-024 | SharePoint path/permission limits cause migration failures | Delays, data access issues (3) | H | High | Pre-scans; restructure; Azure Files for exceptions | Leave share read-write on-prem temporarily (exception) | Data |
| R-025 | Universal Print job volume exceeds licensed allowance; badge-release integration gaps | Cost overrun or printing disruption (3) | M | Medium | Volume estimate from print server logs; vendor integration test | Buy add-on volume; keep print server for affected sites | EP |
| R-026 | AVD/W365 not certified by EHR vendor | Citrix retained; cost (3) | M | Medium | Early vendor engagement; D-10 gated | Citrix DaaS on Azure | EUC |
| R-027 | Node-locked licenses break on device renaming/re-provisioning | App unusable (3) | M | Medium | Inventory in §11.6; vendor re-keying ahead of wave | Temporary license server / manual re-key | AO |
| R-028 | macOS research users resist Intune move (Jamf) | Delay, shadow IT (2) | M | Low | Co-design; D-08 criteria | Retain Jamf Cloud with Intune compliance integration | EP |

## 26.5 Program, people, and commercial risks

| ID | Risk | Impact | Prob. | Severity | Mitigation | Contingency | Owner |
|---|---|---|---|---|---|---|---|
| R-029 | Key-person dependency (1–2 engineers hold MECM/AD/PKI knowledge) | Delay, outage risk (4) | H | **Critical** | Knowledge capture sprint M1–M2; pairing; documentation in runbooks; retention incentives | Partner backfill; Microsoft Unified Support | PM |
| R-030 | BAU workload consumes program capacity | Schedule slip (4) | H | **Critical** | D-07 backfill (6 FTE); protected program time; partner | Re-baseline; reduce wave size | ES |
| R-031 | Budget/licensing approval delayed (D-01) | E5-dependent controls deferred (4) | M | High | Business case tied to cyber insurance and HIPAA risk analysis | E3-only design path (all [E5] items have alternatives documented) | ES |
| R-032 | Partner quality issues (packaging/migration) | Rework (3) | M | Medium | SOW with acceptance criteria, quality gates, sample audits | Replace partner; in-source critical items | PM |
| R-033 | Competing initiatives (EHR upgrade, acquisitions, network refresh) clash with waves | Schedule conflict (3) | H | High | Integrated enterprise change calendar; PMO coordination | Shift waves; resource sharing agreements | PM |
| R-034 | Clinic acquisitions add unplanned forests/devices | Scope growth (3) | M | Medium | Cloud Sync for disconnected forests; acquisition onboarding playbook (Autopilot) | Separate acquisition wave | AR |
| R-035 | Service Desk overwhelmed during waves | Poor experience, reputation (4) | M | High | Wave size cap; hypercare staffing; KB; Remote Help | Pause waves; surge staffing from partner | SD |
| R-036 | Scope creep (server migration, EHR changes pulled in) | Delay (3) | M | Medium | Non-goals (§4.4); Design Authority scope control | Change request with funding | PM |

## 26.6 Compliance and continuity risks

| ID | Risk | Impact | Prob. | Severity | Mitigation | Contingency | Owner |
|---|---|---|---|---|---|---|---|
| R-037 | Audit evidence gaps during transition (HITRUST r2 assessment mid-program) | Certification delay (4) | M | High | Map controls to new evidence sources early (§28); assessor briefing | Compensating controls documentation; assessment timing negotiation | SE + Compliance |
| R-038 | PCI scope change (kiosks/registration) not reassessed | PCI non-compliance (4) | L | Medium | QSA engaged before W9; segmentation validated | Delay kiosk wave | SE |
| R-039 | Service outage of Microsoft cloud during clinical hours | Productivity loss; EHR unaffected (Citrix/on-prem) (3) | L | Low | Resilience design; WHfB cached logon; downtime procedures | Microsoft Service Health comms; clinical downtime procedures | SD |
| R-040 | Retention/legal hold violated by archive/delete decisions (D-05) | Legal/regulatory exposure (5) | L | High | Legal sign-off, hold checks, manifests, immutable archive | Restore from immutable archive | Legal + Data |

## 26.7 Heat map (current residual view, M1)

| | Impact 1–2 | Impact 3 | Impact 4 | Impact 5 |
|---|---|---|---|---|
| **H** | | R-009, R-020, R-024, R-033 | R-005, R-029, R-030 | R-001, R-002 |
| **M** | R-028 | R-008, R-014, R-016, R-022, R-025, R-026, R-027, R-032, R-034, R-036 | R-003, R-007, R-012, R-017, R-019, R-021, R-023, R-031, R-035, R-037 | R-004, R-006, R-010, R-013, R-018 |
| **L** | | R-039 | R-015, R-038 | R-011, R-040 |

> Every row's severity follows the matrix in §26.1. Re-score monthly at the Risk Review (§27.3); the heat map is regenerated from the RAID log.
