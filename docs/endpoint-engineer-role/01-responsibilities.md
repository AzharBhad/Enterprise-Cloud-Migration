# Endpoint Desktop Engineer, Part 1: Responsibilities (Before, During, After)

> **My Role** · EE-1 to EE-3 · Written for the Endpoint Desktop Engineer on the program, who has advanced hands-on Intune and Entra ID experience
>
> In this program you aren't "the person who packages apps". You're the **technical owner of the device experience**, the person who turns the architecture in Part B into something that works on 18,000 real devices. The program's credibility with users depends on your work more than on anyone else's.

## EE-0. Your position in the program

| You are… | In RACI terms |
|---|---|
| **Responsible** for Intune configuration, Autopilot, co-management workloads, Win32 packaging standards, Autopatch, GPO → Intune migration, device-side validation | EP (R) across §10, §11, §14, §17, §18 |
| **Accountable** (as EP lead or senior engineer) for endpoint workstream outcomes | EP (A) for most endpoint rows |
| **Consulted** on identity, certificates, security baselines, network, data migration | C in §9, §13, §16, §12 |
| The **bridge** between architecture (AR), security (SE), and the Service Desk (SD) | You translate designs into runbooks and runbooks into KB articles |

---

## EE-1. Responsibilities before migration (M1–M5: Discover, Design, Build)

### EE-1.1 Discovery and baseline

| # | Responsibility | Output | Due |
|---|---|---|---|
| 1 | Enable **tenant attach** and **Endpoint Analytics** for MECM devices; capture a **baseline** (startup, app reliability, Win11 readiness) | Baseline report (screenshot + export), stored for KPI comparison | M1 |
| 2 | Export full **device inventory** (MECM, Intune, Jamf, MDE): model, age, TPM, OS build, Win11 capability, ownership, site | Device master list with Path A/B/C/D classification (§5.3) | M2 |
| 3 | **Win10 count + ESU status** → feed R-001 | Win10 list with plan per device | Week 1 |
| 4 | Export **GPOs** → import into **Group Policy Analytics**; MDM support report | GPA report + classification sheet (§14.3) | M2–M3 |
| 5 | Export **MECM application catalog**, deployments, metering → app register skeleton | App register v0.1 | M2 |
| 6 | Run endpoint **dependency scans** (scheduled tasks under domain accounts, UNC paths, `%LOGONSERVER%`, mapped drives, local admins, LAPS state, BitLocker escrow status) through MECM run scripts/CMPivot or Intune remediation in detect-only mode | Dependency findings into §15 register | M2–M3 |
| 7 | Inventory **peripherals** per clinical workstation type (signature pads, scanners, label printers, badge readers) | Peripheral matrix for sim lab | M3 |
| 8 | Capture **knowledge** from MECM/OSD experts (task sequences, scripts, collections, baselines) | Runbooks; "what does MECM do for us today" list for the decommission gate (§5.7) | M2 |

### EE-1.2 Design and build

| # | Responsibility | Output | Due |
|---|---|---|---|
| 9 | Intune **tenant architecture**: RBAC roles, scope tags, naming, assignment filters, groups/rings, multi-admin approval | Design doc + implemented config (§10.2) | M3 |
| 10 | **Config-as-code** repo; nightly export; PR process | Git repo, pipeline, README | M3 |
| 11 | Build **Windows baseline**: security baseline, endpoint security (AV, firewall, ASR audit, account protection, disk encryption, LAPS), Edge, OneDrive/KFM, M365 Apps, settings catalog policies | Ring 0 profiles (§10.3.1) | M3–M4 |
| 12 | Build **compliance policies** for all platforms; set the tenant "no policy = non-compliant" setting | §10.5 implemented | M3 |
| 13 | Upgrade **ConfigMgr to 2609**, deploy **CMG**, enable **co-management** (auto-enroll pilot) | Co-mgmt dashboard green for pilot | M2–M3 |
| 14 | **Autopilot**: profiles, ESP, device naming, group tags, OEM registration process, device preparation policy | E2E-01..04 pass (§21.3) | M4 |
| 15 | With identity/network: **Cloud PKI SCEP + Wi-Fi/wired profiles**; prove first Entra-joined device on corporate Wi-Fi | ⭐ M4 milestone (§16.5) | M4 |
| 16 | With identity: **WHfB cloud Kerberos trust** policy; validate Kerberos SSO to file shares and IWA apps | E2E-05/07 pass | M4 |
| 17 | Packaging **standards** (PSADT v4, detection, logging, naming, supersedence) and factory onboarding | Packaging standard doc; first 50 apps | M4 |
| 18 | **Autopatch** enrollment, groups/rings, driver and firmware policies | Autopatch groups mapped to rings | M4 |
| 19 | **Remote Help**, **EPM** (if E5), **Windows LAPS** for SD | SD roles configured | M5 |
| 20 | **Post-migration health script** (§21.5) and **KFM status** remediation (detect-only) | Scripts in Git; output in Log Analytics | M4 |
| 21 | Write **runbooks**: Path B conversion, Autopilot troubleshooting, workload switch, rollback per §22.2 | Runbook library | M5 |
| 22 | **Train the Service Desk** (SD2–SD4, SD7 modules in §25.3) | Trained Tier 1/2 | M5 |

---

## EE-2. Responsibilities during migration (M5–M15: Pilot, Scale, Decommission)

### EE-2.1 Per-wave responsibilities

| When | Responsibility |
|---|---|
| T-6 to T-5 weeks | Validate the wave device list; classify Path A/B/C/D; check app readiness for the wave's app set; flag peripherals and Path D candidates |
| T-3 weeks | Enable **KFM** for wave users (throttled); **Convert-all Autopilot** registration for wave devices; assign Autopilot profile; verify `Assigned` status |
| T-1 week | KFM health remediation; confirm ESP app success rates for the wave's apps in Ring 2; check Autopatch/feature update state; prepare spare devices |
| T-2 days | Present **endpoint readiness** at go/no-go: KFM %, profile assignment %, app readiness %, open defects |
| T-0 | Execute tranche wipes (MAA approvals); watch Autopilot/ESP dashboard live; first-line escalation for technicians; GPO unlink for the wave group |
| T+1 to T+5 | Daily defect triage; root-cause analysis; fix forward through rings; update KB/runbooks |
| T+10 | Wave endpoint metrics in the closure report; stale Hybrid/AD object cleanup scheduled |

### EE-2.2 Continuous responsibilities during the program

| Area | Responsibility |
|---|---|
| **Co-management workloads** | Move workloads per §18 sequence; monitor `CoManagementHandler.log` patterns at scale; promote pilot → all with KPI evidence |
| **GPO migration** | Per ring: build/validate Intune equivalent → unlink GPO → verify → log; maintain gap register |
| **App pipeline** | Triage factory output; enforce standards; supersedence; retire MECM deployments as Intune versions go live |
| **Security baselines** | Move ASR from audit to block by ring with SE; tune exclusions from Advanced Hunting data; migrate BitLocker from MBAM (escrow first!) |
| **Autopatch** | Monitor release health each Patch Tuesday; pause/resume rings; expedite zero-days on SE request |
| **Mobile/macOS** | Android DA → AE re-enrollment schedule; iOS shared device mode; macOS ADE + Platform SSO (if D-08 = Intune) |
| **Change control** | Every production change through PR + CAB; test tenant first for compliance/CA-impacting changes |
| **Reporting** | Daily wave tracker; weekly technical dashboard; monthly KPI pack for PMO |
| **Defect management** | Own endpoint defects S2–S4; join S1 war rooms; write post-incident reviews |
| **Decommission** | MECM client removal; reporting migration; decommission gate evidence (§5.7); DP/SUP/WSUS removal; PXE removal |

### EE-2.3 Escalation responsibilities

| Situation | Your action |
|---|---|
| Autopilot failure rate > 5 % in a wave | Pause tranches; pull diagnostics from 5 failing devices; fix ESP/app; inform PM |
| Compliance drop > 2 % day-over-day | Identify the setting/policy; check service health; roll back profile if self-inflicted |
| Clinical issue on swapped device | Immediate swap to old/spare device; then investigate (patient care first) |
| Security request (zero-day) | Expedite update / ASR rule through emergency change |

---

## EE-3. Responsibilities after migration (M15+: Optimize, Operate, Evolve)

| # | Responsibility | Cadence |
|---|---|---|
| 1 | **Platform owner** for Intune/Autopilot/Autopatch: service health, Message Center triage, Intune monthly service release review | Daily/weekly/monthly |
| 2 | **Endpoint experience management**: Endpoint Analytics + Advanced Analytics review, Remediations library growth, anomaly investigation | Weekly |
| 3 | **Configuration hygiene**: drift detection, policy consolidation, remediation script debt reduction, exclusion cleanup | Monthly |
| 4 | **Lifecycle**: Autopilot hygiene, stale device cleanup, token/certificate expiry, OEM registration process | Monthly/quarterly |
| 5 | **Windows feature update** planning (annual), baseline version upgrades | Annual |
| 6 | **Security partnership**: Vulnerability management remediation (apps + OS), EPM rule tuning, App Control expansion | Weekly/monthly |
| 7 | **Automation**: Graph-based lifecycle and reporting automation; Copilot in Intune adoption (with governance) | Ongoing |
| 8 | **Path D exceptions** review and conversion | Quarterly |
| 9 | **Phase 6 support**: device-side input to AD retirement (remaining dependencies) | M18+ |
| 10 | **Knowledge and coaching**: Tier 2 coaching, KB quality, onboarding new engineers | Ongoing |
