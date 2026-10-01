# 4. Business Objectives

> **Part A: Strategy** · Section 4 of 33

## 4.1 Objectives hierarchy

Every technical decision in this blueprint must trace back to at least one business objective. Workstream leads use this table to justify scope and to push back on scope creep.

| ID | Business objective | Why it matters to a healthcare network | Primary workstreams |
|---|---|---|---|
| **BO-1** | **Reduce cyber risk to patient care** | Ransomware in healthcare causes ambulance diversion and delayed care. Identity compromise is the dominant entry path. | Identity (§9), Security (§13), Certificates (§16) |
| **BO-2** | **Sustain and evidence regulatory compliance** | HIPAA Security Rule, HITRUST r2, PCI DSS, and Part 11. OCR enforcement and the proposed HIPAA Security Rule updates point to stronger MFA, encryption, and asset inventory expectations. | Security (§13), Compliance (§28) |
| **BO-3** | **Improve clinician and staff productivity** | Every minute spent on login, VPN, or a broken device is a minute away from patients. | Endpoint (§10), Autopilot (§17), Data (§12) |
| **BO-4** | **Enable a flexible, distributed workforce** | Remote coding/billing, telehealth, affiliated physicians, new clinic acquisitions | Identity (§9), Endpoint (§10), VDI |
| **BO-5** | **Reduce infrastructure cost and technical debt** | Avoid the M14 datacenter refresh and cut on-prem server count and admin toil | All, especially Infra decommission (Phase 4) |
| **BO-6** | **Increase IT agility and resilience** | Onboard an acquired clinic in days, not months. Recover from device loss or ransomware by re-provisioning from the cloud. | Autopilot (§17), DR (§22) |
| **BO-7** | **Improve operational visibility** | Leadership asks "are we secure and patched?" and gets an answer in hours. Target: real-time. | Monitoring (§32), Dashboards (App-E/F) |

## 4.2 Objectives → key results (OKRs)

| Objective | Key result | Baseline | M6 | M12 | M18 | Source of truth |
|---|---|---|---|---|---|---|
| BO-1 | % users registered for phishing-resistant method | ~0 % | 30 % | 75 % | ≥ 90 % (100 % admins) | Entra Authentication Methods activity |
| BO-1 | Standing privileged role assignments (Entra) | 11 GA permanent | ≤ 4 | 2 break-glass only | 2 break-glass only | PIM report **[E5]** |
| BO-1 | Domain Admins | 38 | 15 | 6 | ≤ 4 | AD group membership |
| BO-1 | Devices with MDE "Active" sensor + EDR in block mode | ~75 % | 95 % | 99 % | 99.5 % | Defender portal |
| BO-2 | % in-scope endpoints with BitLocker/FileVault + key escrowed to cloud | ~85 % (MBAM) | 90 % | 98 % | 99.5 % | Intune encryption report |
| BO-2 | Endpoints in an authoritative inventory (any platform) | ~85 % | 92 % | 98 % | ≥ 99 % | Intune + MDE device inventory reconciliation |
| BO-3 | Median boot-to-desktop (Endpoint Analytics) | baseline M1 | –10 % | –25 % | –35 % | Endpoint Analytics |
| BO-3 | Shared workstation badge-tap to EHR ready | ~45 s (est.) | baseline | ≤ 30 s | ≤ 20 s | Imprivata / clinical timing study |
| BO-4 | Users needing VPN for daily work | ~6,000 | 4,500 | 1,500 | ≤ 300 | VPN concurrency logs |
| BO-5 | On-prem servers supporting EUC (DCs, DPs, MECM, file, print, AD FS, CA) | ~165 | 160 | 70 | ≤ 15 | CMDB |
| BO-6 | New-hire device ready on day 1 (zero-touch) | ~40 % | 60 % | 85 % | ≥ 95 % | Autopilot deployment report + HR start-date join |
| BO-7 | Executive dashboard automated refresh | Manual monthly | Weekly | Daily | Near-real-time | Power BI (App-E) |

## 4.3 Financial objectives (indicative)

| Lever | Mechanism | Order of magnitude |
|---|---|---|
| Hardware avoidance | No refresh of MECM, DP, file, and print server hardware at M14 | ~120 servers × refresh cost |
| Storage | 70 TB archived/deleted instead of migrated; tiered SAN retirement | Storage refresh avoided |
| Licensing consolidation | Retire legacy EDR (M9), third-party remote support tool, Jamf on-prem infrastructure (if Intune selected), MFA SMS gateway | Contract-level savings |
| Labor | Zero-touch provisioning; fewer imaging/break-fix site visits; automated patching | ~6–8 FTE capacity redirected (not cut) |
| Risk | Lower probability of a ransomware event | Avoided cost of a breach. Healthcare breach costs are the highest of any industry in annual industry studies. |

> **[OPINION]** Present the business case to the board as **risk reduction plus capacity redirection**, not headcount reduction. Endpoint and identity teams will be **busier for 18 months**, not less busy, and positioning savings as job cuts damages adoption.

## 4.4 Non-goals (explicitly out of scope)

| Non-goal | Reason |
|---|---|
| Replace the EHR | Separate clinical program; this blueprint only changes how endpoints access it |
| Manage FDA-regulated medical devices with Intune | Vendor-validated configuration. We only remediate their **AD/SMB/DNS dependencies**. |
| Server workload migration to Azure (beyond identity, print connector, file exceptions) | Covered by the separate datacenter migration program; dependencies shared via the RAID log |
| Replace Cisco ISE / network access control | Retained; integrated with Intune |
