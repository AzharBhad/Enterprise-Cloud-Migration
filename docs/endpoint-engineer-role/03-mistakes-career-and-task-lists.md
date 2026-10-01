# Endpoint Desktop Engineer, Part 3: Common Mistakes, Career Growth, and Task Lists

> **My Role** · EE-8 to EE-10

## EE-8. Common mistakes endpoint engineers make in cloud transformations

| # | Mistake | Why it happens | Consequence | Do this instead |
|---|---|---|---|---|
| 1 | **Recreating the golden image in Intune** (dozens of apps in ESP, every GPO ported) | Familiarity with OSD | 2-hour ESP, frequent timeouts, fragile builds | Thin ESP (≤ 6 blocking apps), vanilla OEM image, baseline-first config |
| 2 | **Porting GPOs 1:1** | GPA "Migrate" button feels like the job | Contradictory settings, conflicts, tech debt in a new platform | Migrate intent; classify (§14.3); retire ~20 % |
| 3 | **Forgetting to unlink GPOs** after moving a setting area to Intune on co-managed devices | Two teams, two consoles | Conflicts; "MDM vs GPO" flip-flopping; unpredictable state | Same-day unlink/security-filter per ring; `MDMWinsOverGP` only as a safety net |
| 4 | **Treating Hybrid join as the destination** | Feels lower-risk | Permanent AD dependency, slow Autopilot, no path to cloud-only | Hybrid is a bridge with an expiry date (§5.6) |
| 5 | **Assuming Entra join "just works" with on-prem resources** | Cloud Kerberos trust demos work in the lab | Machine-account auth, NTLM, LDAP, GPO-delivered app settings break in production | Dependency telemetry (§15) before waves; test on real apps |
| 6 | **Switching the Endpoint Protection workload before escrowing BitLocker keys** | Sequencing oversight | Devices with no recoverable key → data loss or a reportable breach | Escrow remediation + verification report first (§13.3.3) |
| 7 | **Mixing MSI LOB and Win32 apps in Autopilot** | Convenience | ESP failures (TrustedInstaller conflicts) | Win32 only |
| 8 | **Assigning to "All devices/All users" during pilots** | Speed | Pilot changes hit production | Rings + filters; multi-admin approval for "All" assignments |
| 9 | **Ignoring the network team** | Endpoint vs. network silos | TLS inspection breaks Intune/Autopilot; Wi-Fi certs fail; DO not configured | Joint design in M2–M4; network prerequisites PR-N1…N11 |
| 10 | **No configuration backup/version control** | "The portal is the source of truth" | Can't roll back or prove what changed (audit) | Config-as-code, daily export, PR review |
| 11 | **Over-using remediation scripts** | Quick wins | Unmaintainable script estate; performance impact | Script debt register; prefer CSP/settings catalog |
| 12 | **Testing as admin on lab devices only** | Convenience | Real users (standard user, CA-enforced, slow WAN) fail | Test as standard user, on real network, with production CA |
| 13 | **Under-communicating with the Service Desk** | Focus on engineering | Ticket surge; SD erodes user trust | SD in pilots from day 1; KB before each wave |
| 14 | **Skipping KFM verification before wipe** | Schedule pressure | Data loss incidents; trust destroyed | KFM hard gate (§17.5) |
| 15 | **Not watching tokens and certificates** (APNs, ADE, VPP, CMG cert, connectors) | No owner | Sudden platform-wide outage | Expiry dashboard (R12), shared ownership |
| 16 | **Treating clinical devices like office devices** | One-size-fits-all builds | Clinical disruption; loss of clinical sponsorship | Separate persona, ring, pilot, blackout rules |
| 17 | **Chasing feature novelty mid-program** | New features every month | Scope creep, instability | Change radar; adopt through Design Authority, not on impulse |
| 18 | **Not measuring a baseline** | Rush to build | Can't prove improvement (business case) | Endpoint Analytics baseline in M1 |
| 19 | **Leaving pilot exclusions in place** | Forgotten | Security gaps | Exclusions with expiry; weekly review |
| 20 | **Becoming the single point of failure** | You're the expert | Burnout; program risk (R-029) | Document, pair, delegate, automate |

## EE-9. Career growth opportunities

### EE-9.1 Roles this project prepares you for

| Role | What this program gives you | Gap to close |
|---|---|---|
| **Senior / Lead Endpoint Engineer** | Ownership of Intune/Autopilot/Autopatch at 18k-device scale | Leadership of a small team; vendor management |
| **Modern Workplace / Endpoint Architect** | Design decisions (Hybrid vs. Entra join, PKI, co-mgmt sequencing), decision papers, Design Authority participation | Broader architecture (identity, network, M365 workloads); write ADRs |
| **Cloud Identity & Access Engineer** | Hands-on CA, WHfB, CBA, cloud Kerberos trust, device identity | Deeper Entra (PIM, governance, Cloud Sync) |
| **Endpoint Security Engineer** | MDE, ASR, EPM, App Control, LAPS, compliance-driven access | Threat hunting (KQL), incident response |
| **Platform / Automation Engineer (Graph, DevOps)** | Config-as-code, Graph automation, dashboards | CI/CD pipelines, software engineering practices |
| **Technical Program / Service Owner (End-User Computing)** | Wave execution, KPIs, governance exposure | Financial management, service management (ITIL) |
| **Microsoft MVP / community contributor** (stretch) | Real-world lessons at scale | Public writing/speaking (blog, GitHub, user groups) |

### EE-9.2 Certification roadmap

| Order | Certification | Relevance | When |
|---|---|---|---|
| 1 | **MD-102: Endpoint Administrator** | Validates core Intune/Autopilot/Windows skills | M3–M6 |
| 2 | **SC-300: Identity and Access Administrator** | Entra, CA, PIM, device identity | M6–M9 |
| 3 | **MS-102: Microsoft 365 Administrator** | Tenant-wide administration, security & compliance | M9–M12 |
| 4 | **AZ-104: Azure Administrator** | Log Analytics, Azure Files, AVD foundations | M12–M15 |
| 5 | **SC-200: Security Operations Analyst** *(security path)* or **AZ-140: Azure Virtual Desktop Specialty** *(EUC path)* | Specialization | M15–M18 |
| 6 | **SC-100: Cybersecurity Architect Expert** *(architect path)* | Zero Trust architecture | Year 2 |

> Microsoft regularly retires, renames, and introduces exams (including newer Copilot/AI-related credentials). Check Microsoft Learn's current certification list before you schedule.

### EE-9.3 Portfolio you'll have at the end

- A **public, sanitized version** of this blueprint and your scripts (this GitHub repository), which is valuable for interviews and community credibility. **Never** publish tenant IDs, internal hostnames, or PHI.
- Measured **outcomes** with numbers: provisioning time, compliance %, patch currency, ticket reduction, servers retired.
- **Decision papers / ADRs** you authored.
- **Runbooks and automation** (Graph/KQL) you built.

## EE-10. Daily, weekly, and monthly task lists

### EE-10.1 Daily (≈ 60–90 min of routine; more on wave days)

| ✓ | Task | Tool |
|---|---|---|
| ☐ | Check **M365 Service Health** for Intune, Entra, Autopatch, Windows, Exchange/Teams/SPO | M365 admin center / Teams alert channel |
| ☐ | Review **Autopilot/ESP failures** from the last 24 h; open defects for patterns | Autopilot report (R5) |
| ☐ | Review **compliance changes** (drops > 1 %, new non-compliance reasons) | R4 |
| ☐ | Check **app install failures** for newly deployed/updated apps | R7 |
| ☐ | Check **connector/token health** (Cert Connector, Private Network Connectors status with ID team, APNs/ADE/VPP) | R12 |
| ☐ | Triage **escalated tickets** from Tier 2; update KB if new pattern | ITSM |
| ☐ | **Wave days:** live wave tracker, tranche approvals (MAA), technician support, end-of-day wave report | R1 |
| ☐ | Review **pending EPM requests** escalations and **multi-admin approvals** waiting on you | Intune |
| ☐ | Commit any configuration changes to Git; check nightly export ran | Git |

### EE-10.2 Weekly

| ✓ | Task | Day (suggested) |
|---|---|---|
| ☐ | **Message Center triage**: Intune/Windows/Entra changes → backlog items | Monday |
| ☐ | **CAB** submissions and attendance | Tuesday |
| ☐ | **Patch Tuesday** (2nd Tuesday): Autopatch release monitoring, ring progression, known issues (Windows release health) | Tue–Thu |
| ☐ | **Wave readiness** reviews for upcoming waves (app readiness, KFM %, profile assignment) | Wednesday |
| ☐ | **Co-management workload** progress and promotion decisions | Wednesday |
| ☐ | **GPO migration** burn-down update; unlink actions completed | Thursday |
| ☐ | **Drift report** (prod vs. Git) and resolution | Thursday |
| ☐ | **ASR / Defender** tuning review with SE (audit hits, exclusions) | Thursday |
| ☐ | **Technical dashboard** update and weekly status to PMO | Friday |
| ☐ | **Knowledge**: update runbooks/KB from the week's incidents; 1 h learning (Microsoft Learn, certification prep) | Friday |

### EE-10.3 Monthly

| ✓ | Task |
|---|---|
| ☐ | **Intune service release** (`YYMM`) notes review → impact assessment → test in Ring 0 |
| ☐ | **KPI pack** for Steering/PMO (EE-7 KPIs with trend) |
| ☐ | **Endpoint Analytics / Advanced Analytics** review: top startup/app reliability offenders; new remediation candidates |
| ☐ | **Stale device cleanup** (Intune cleanup rules, Entra stale devices, Autopilot hygiene) |
| ☐ | **Exceptions register** review: Path D, remediation scripts, CA/ASR/compliance exclusions; expire or renew with justification |
| ☐ | **Security baseline / CIS** comparison; new baseline versions |
| ☐ | **Token & certificate expiry** look-ahead (90 days) |
| ☐ | **App lifecycle**: retire unused apps (install counts), supersede outdated versions, EAM catalog updates |
| ☐ | **License reconciliation** input (device-licensed SKUs, shared devices) |
| ☐ | **DR/restore test** (rotating: config restore to test tenant, Autopilot rebuild drill, BitLocker recovery) |
| ☐ | **Retrospective**: what slowed us down this month; one automation to build next month |

### EE-10.4 Quarterly / annual (for completeness)

| Cadence | Task |
|---|---|
| Quarterly | RBAC/role access review for Intune admins; Remote Help role review; Path D exception reviews; DR test; roadmap check against Microsoft roadmap |
| Annual | Windows feature update planning; Intune architecture review; APNs/ADE renewals; certification renewal (Microsoft certifications renew annually through free online assessment) |
