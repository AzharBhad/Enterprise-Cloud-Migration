# 17. Autopilot Deployment Strategy

> **Part B: Workstream Plans** · Section 17 of 33 · Lead: Endpoint Team · Related: §5.3 device paths, §11 ESP apps, §16 certs

## 17.1 Objectives

| KPI | Target |
|---|---|
| Autopilot first-attempt success rate | ≥ 95 % (≥ 98 % by M12) |
| Median user-driven provisioning time (box → desktop) | ≤ 45 min (pre-provisioned: ≤ 15 min user phase) |
| New devices OEM-registered at purchase | 100 % from M5 |
| PXE/OSD builds | 0 after M12 |

## 17.2 Scenario selection by persona

| Persona | Autopilot scenario | Join | Profile name | Why |
|---|---|---|---|---|
| Knowledge worker (new/refresh) | **User-driven** (classic) **or Autopilot device preparation** | Entra | `AP-UD-Standard` / `DP-Standard` | Zero-touch, ships direct to user |
| Knowledge worker, slow-bandwidth clinic | **Pre-provisioned** (technician flow at depot) | Entra | `AP-PP-Standard` | Apps preloaded; user phase short |
| Physicians / execs (white-glove) | Pre-provisioned | Entra | `AP-PP-VIP` | Experience |
| Shared clinical workstation | **Pre-provisioned** (device phase) then shared-device sign-ins | Entra (target) / Hybrid (Path D bridge, *not via Autopilot*) | `AP-PP-SharedClinical` | Device-targeted apps and certs before go-live on the unit |
| Kiosk / signage / check-in | **Self-deploying** (TPM attestation) | Entra | `AP-SD-Kiosk` | No user; Assigned Access |
| In-life device conversion (Path B) | **Intune Wipe → Autopilot user-driven / pre-provisioned** | Hybrid → Entra | Same as persona | Supported route from Hybrid to Entra join |
| Re-purpose an Entra-joined device | **Autopilot Reset** (remote) | Entra (kept) | — | Keeps enrollment; fast |
| Teams Rooms / HoloLens | Self-deploying | Entra | `AP-SD-MTR` | Device-specific |

### 17.2.1 Classic Autopilot vs. Autopilot device preparation

| Criterion | Classic Autopilot (v1) | Autopilot device preparation (v2) |
|---|---|---|
| Registration | Hardware hash required (OEM/CSV) | **Not required** (device association optional) |
| Modes | User-driven, pre-provisioned, self-deploying, existing devices | User-driven, automatic (W365) |
| Join | Entra and Hybrid | **Entra only** |
| Experience | ESP (configurable, can be slow) | Faster, fewer components, near-real-time reporting |
| Gaps | — | No pre-provisioning or self-deploying; fewer customizations |
| **Contoso use** | **Primary** (pre-provisioning, self-deploying, registration-based control) | **Knowledge-worker user-driven pilot from M5**; expand if success rate ≥ classic |

> **[OPINION]** Keep classic Autopilot as the backbone. Hardware-hash registration is also a **security control**: only registered devices can join the tenant through Autopilot. Pair it with **corporate device identifiers / enrollment restrictions** that block personal Windows enrollment.

## 17.3 Registration strategy

| Source | Method | Group tag |
|---|---|---|
| New purchases (Dell/HP/Lenovo) | **OEM/reseller registration** against tenant (Partner Center / CSP relationship) with **group tag by order** | Set at order: `KW`, `SHC`, `KSK`, `VIP` |
| In-life Path B devices (co-managed) | Intune Autopilot deployment profile with **"Convert all targeted devices to Autopilot" = Yes**, targeted at a dynamic group of co-managed devices in the wave | Assigned through Graph script post-import |
| Exceptions / repairs (motherboard swap) | `Get-WindowsAutopilotInfo -Online` by technician / OEM re-registration | As per persona |
| Group-tag → profile mapping | Dynamic device groups: `(device.devicePhysicalIDs -any (_ -eq "[OrderID]:SHC"))` | — |

**Hygiene:** quarterly cleanup of Autopilot devices that are retired or disposed (deregister before disposal or resale, which is mandatory in the asset-disposal SOP).

## 17.4 Profile and ESP design

### 17.4.1 Deployment profile settings (`AP-UD-Standard`)

| Setting | Value |
|---|---|
| Deployment mode | User-driven |
| Join to Entra ID as | Microsoft Entra joined |
| Microsoft Software License Terms | Hide |
| Privacy settings | Hide |
| Hide change account options | Yes |
| User account type | **Standard** |
| Allow pre-provisioned deployment | Yes |
| Language/region | Operating system default (en-US); keyboard auto |
| Apply device name template | `CH-%SERIAL%` (max 15 chars; trim serial if needed). Shared: `CH-SH-%RAND:6%`, kiosk `CH-KS-%RAND:6%` |

### 17.4.2 Enrollment Status Page

| Setting | Standard | Shared clinical | Kiosk |
|---|---|---|---|
| Show app and profile progress | Yes | Yes | Yes |
| Timeout | 60 min | 90 min | 60 min |
| Show custom message on error | Yes: "Call Service Desk x5555, code shown below" | Same | Same |
| Allow log collection | Yes | Yes | Yes |
| Only show for OOBE devices | Yes | Yes | Yes |
| Block device use until required apps installed | **Selected apps only** | Selected apps | Selected apps |
| Blocking apps (≤ 6) | Company Portal, M365 Apps, Citrix Workspace, GSA client, Defender config (policy), LOB baseline pack | + EHR peripheral drivers, tap-badge agent | Kiosk app |
| Allow reset on error | Yes | Yes | Yes |
| Allow use if error | No | No | No |
| **Skip user status page** | **Yes** (account setup phase skipped for user-targeted apps; they install at the desktop) | n/a | n/a |

> **ESP pitfalls to avoid:** (1) mixing MSI LOB and Win32 apps in ESP; (2) blocking on apps with user-context dependencies; (3) BitLocker policy that starts encryption before escrow; (4) Windows Update quality updates during OOBE (Win11 OOBE may install them; plan for the extra time or configure ESP accordingly); (5) policies requiring reboot mid-ESP (some CSPs, e.g., certain Device Guard settings). **Keep the ESP thin.**

## 17.5 Path B: in-life conversion runbook (per device)

| Step | Action | Control |
|---|---|---|
| 1 | Device in wave group; co-managed; Autopilot hash registered (Convert-all); profile assigned (`Assigned` status) | Wave tracker check |
| 2 | **KFM verified** (Desktop/Documents/Pictures protected, sync up to date) through remediation script result | **Hard gate** |
| 3 | Local data check: no data outside known folders (scan `C:\Data`, `C:\Users\*\Downloads`, app data for critical apps); user attestation through Company Portal notification/email | Soft gate |
| 4 | User comms: T-5 days, T-1 day, T-0 (scheduled window) | Comms plan (§24) |
| 5 | **Intune → Wipe** (not "retain enrollment state") at scheduled time (bulk wipe needs **multi-admin approval**) | MAA |
| 6 | OOBE → Autopilot user-driven → Entra join → ESP → desktop | Autopilot report |
| 7 | User signs in with WHfB setup (TAP if needed) | Desk-side or self-service |
| 8 | Post-checks: compliance, Wi-Fi cert, apps, printers, OneDrive sync, EHR access | Automated health script + user checklist |
| 9 | Clean-up: delete stale **Hybrid** Entra device object and AD computer object (after 14 days), remove from MECM | Scheduled script |

**Throughput:** 300–500 Path B devices/day per wave (limited by Service Desk capacity and the bandwidth plan with Delivery Optimization/Connected Cache).

## 17.6 Network and content delivery for Autopilot

| Item | Design |
|---|---|
| Content | Win32 apps from Intune CDN; **Delivery Optimization** (LAN peering, `DODownloadMode=2` group by site); **Microsoft Connected Cache** at hospitals/large clinics |
| OOBE network | Onboarding SSID / wired VLAN with internet only + required Microsoft endpoints (Intune, Autopilot, Entra, WNS, MDE, Windows Update, Delivery Optimization, NTP) |
| TLS inspection | **Bypassed** for Autopilot/Intune/Entra/WNS endpoints |
| Depot | Pre-provisioning bench at 2 depot sites with 1 Gbps internet, 40 devices in parallel |

## 17.7 Troubleshooting toolkit

| Tool | Use |
|---|---|
| Intune → **Autopilot deployments** report, ESP report, device preparation deployment report | Fleet view |
| `mdmdiagnosticstool.exe -area Autopilot;TPM -cab C:\temp\ap.cab` | Device logs |
| `Get-AutopilotDiagnosticsCommunity` / `Get-AutopilotDiagnostics.ps1` | Parse ESP/IME timeline |
| `%ProgramData%\Microsoft\IntuneManagementExtension\Logs\IntuneManagementExtension.log`, `AppWorkload.log` | Win32 app installs |
| Event Viewer: `Microsoft-Windows-ModernDeployment-Diagnostics-Provider/Autopilot`, `DeviceManagement-Enterprise-Diagnostics-Provider/Admin` | Enrollment errors |
| TPM attestation: `tpmtool getdeviceinformation` | Self-deploying/pre-provisioning failures |

## 17.8 Autopilot RACI

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Scenario & profile design | I | I | C | C | **A/R** | C | I | C |
| OEM/reseller registration contracts | C | R | C | I | **A** | I | I | I |
| ESP app selection | I | I | C | I | **A/R** | C | C | C |
| Onboarding network | I | I | **A** | I | C | C | I | I |
| Path B runbook execution | I | C | I | I | **A/R** | I | I | R |
| Depot pre-provisioning | I | C | I | I | **A** | I | I | R |
| Autopilot hygiene (dereg on disposal) | I | I | I | I | **A**/R | C | I | R (asset mgmt) |
