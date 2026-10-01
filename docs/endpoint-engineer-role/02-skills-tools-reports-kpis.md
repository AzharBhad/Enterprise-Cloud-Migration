# Endpoint Desktop Engineer, Part 2: Skills, Tools, Reports, and KPIs

> **My Role** · EE-4 to EE-7

## EE-4. Technical skills required

Legend: **Core** = within advanced Intune/Entra skills you already have · **Stretch** = adjacent, deepen it · **Beyond** = outside core Intune/Entra, a new skill for this program

| # | Skill | Level needed | Category | Why it matters here |
|---|---|---|---|---|
| 1 | Intune configuration (settings catalog, endpoint security, compliance, filters, scope tags, RBAC) | Expert | **Core** | Everything |
| 2 | Windows Autopilot (classic + device preparation), ESP troubleshooting | Expert | **Core** | §17 |
| 3 | Win32 app packaging (PSADT v4, detection, supersedence, dependencies) | Expert | **Core / Stretch** | §11 |
| 4 | Entra device identity (join types, PRT, `dsregcmd`), CA interaction with compliance | Advanced | **Core** | Troubleshooting sign-in/compliance |
| 5 | **Configuration Manager**: co-management, CMG, tenant attach, client logs, collections, client removal | Advanced | **Beyond** (if not already) | §18; the bridge depends on it |
| 6 | **Group Policy deep knowledge**: RSoP, loopback, GPP, WMI filters, tattooing, ADMX | Advanced | **Beyond** | §14; you can't migrate what you can't read |
| 7 | **Kerberos/NTLM/LDAP fundamentals**: cloud Kerberos trust, SPNs, `klist`, KDC proxy concepts | Intermediate–Advanced | **Beyond** | Diagnosing "SSO broken" on Entra-joined devices (§15) |
| 8 | **PKI**: SCEP vs. PKCS, Cloud PKI, cert chains, EKUs, CRL/AIA, TPM key storage | Intermediate–Advanced | **Beyond** | §16, Wi-Fi critical path |
| 9 | **Networking for endpoints**: 802.1X/EAP-TLS, Cisco ISE basics, proxy/TLS inspection, Delivery Optimization, Connected Cache, Entra Private Access client | Intermediate | **Beyond** | Autopilot and Wi-Fi failures are often network failures |
| 10 | **PowerShell + Microsoft Graph** (Graph PowerShell SDK, REST, app registrations with certificates, paging, batching) | Advanced | **Stretch** | Automation, reporting, cleanup at scale |
| 11 | **KQL** (Log Analytics, Defender Advanced Hunting) | Intermediate–Advanced | **Beyond** | Dashboards, ASR tuning, health reporting |
| 12 | **Windows servicing**: Autopatch, WUfB, safeguard holds, hotpatch, driver/firmware management | Advanced | **Stretch** | §10.4 |
| 13 | **Defender for Endpoint**: onboarding, AV modes, ASR, tamper protection, Device Control, security settings management | Intermediate–Advanced | **Stretch** | §13.3 |
| 14 | **Windows security internals**: BitLocker, Credential Guard, LAPS, App Control for Business (managed installer), EPM | Advanced | **Stretch** | §13.3 |
| 15 | **macOS management**: ADE, Platform SSO, DDM, FileVault, PPPC/system extensions | Intermediate | **Stretch / Beyond** | §10.3.5 |
| 16 | **Android Enterprise/iOS**: dedicated devices, shared device mode, OEMConfig, MAM | Intermediate–Advanced | **Stretch** | §10.3.7–8 |
| 17 | **OneDrive/KFM and SharePoint migration** basics (Migration Manager, sync health) | Intermediate | **Beyond** | Path B gate depends on KFM |
| 18 | **Universal Print** | Basic–Intermediate | **Beyond** | §12.7 |
| 19 | **Azure fundamentals** (Log Analytics, Azure Files, AVD/W365, landing zone basics) | Intermediate | **Beyond** | Monitoring, VDI, Azure Files exceptions |
| 20 | **Git / config-as-code / CI** (Microsoft365DSC, IntuneManagement, Azure DevOps/GitHub) | Intermediate | **Beyond** | §31.5 |
| 21 | **Power BI** (data modeling from Graph/Log Analytics) | Basic–Intermediate | **Beyond** | Dashboards (App-E/F) |
| 22 | **Soft skills**: writing runbooks, presenting at go/no-go, stakeholder management with clinicians, incident leadership | Advanced | **Beyond** | Credibility and influence |

## EE-5. Tools to master

| Category | Tool | What to master |
|---|---|---|
| Admin centers | **Intune admin center**, **Entra admin center**, **Microsoft Defender portal**, **M365 admin center** (Service Health, Message Center, Edge site lists), **Autopatch** (in Intune) | Every blade you touch; reports; troubleshooting pane |
| Device diagnostics | `dsregcmd /status`, `mdmdiagnosticstool.exe`, `MDMDiagReport`, Event Viewer channels (`DeviceManagement-Enterprise-Diagnostics-Provider`, `ModernDeployment-Diagnostics-Provider`, `AAD`), `klist`, `certutil`, `tpmtool`, `gpresult`, Settings → Access work or school → **Export management log files** | Fast root cause on a single device |
| Intune logs | `IntuneManagementExtension.log`, `AppWorkload.log`, `AgentExecutor.log`, `HealthScripts.log` (`%ProgramData%\Microsoft\IntuneManagementExtension\Logs`) + **Collect diagnostics** remote action | Win32 and remediation troubleshooting |
| ConfigMgr | Console, CMPivot, `CoManagementHandler.log`, `ComplRelayAgent.log`, `WUAHandler.log`, `CIAgent.log`, CMTrace/OneTrace | Co-management bridge |
| Autopilot | Autopilot deployment/ESP reports, `Get-WindowsAutopilotInfo`, `Get-AutopilotDiagnosticsCommunity` | Provisioning failures |
| Packaging | **PSAppDeployToolkit v4**, **Win32 Content Prep Tool**, MSIX Packaging Tool, Orca/InstEd, Process Monitor | Repeatable, silent installs |
| Automation | **Microsoft Graph PowerShell SDK**, Graph Explorer, VS Code + Git, **IntuneManagement** (export/import/document), **Microsoft365DSC**, **Maester** (CA/Entra tests) | Config-as-code, bulk operations |
| Analytics | **Log Analytics / KQL**, **Defender Advanced Hunting**, **Endpoint Analytics** & **Advanced Analytics**, **Windows Update for Business reports**, **Power BI** | Dashboards & KPIs |
| Support | **Remote Help**, LAPS retrieval, BitLocker key retrieval, Autopilot reset | SD escalations |
| Mac/mobile | Apple Business Manager, Apple Configurator (for ADE onboarding of non-ABM devices), Managed Google Play, Zebra StageNow/OEMConfig | Platform onboarding |
| Network | Wireshark (basic), ISE live logs (read access), `Test-NetConnection`, Delivery Optimization PowerShell (`Get-DeliveryOptimizationStatus`) | Network-side issues |

## EE-6. Reports and dashboards you should build

### EE-6.1 Dashboard portfolio

| # | Report / dashboard | Audience | Source | Refresh |
|---|---|---|---|---|
| R1 | **Wave tracker** (device-level readiness & status) | PMO, wave leads | Wave list + Entra + Intune + KFM script + Autopilot report (Power BI) | Hourly on wave days |
| R2 | **Technical project dashboard** (App-F) | DA, engineering | Intune Data Warehouse / Graph + Log Analytics | Daily |
| R3 | **Co-management workload status** | EP, DA | MECM (co-mgmt dashboard / SQL views) + Intune `managementAgent` | Daily |
| R4 | **Compliance & security posture** | SE, EP | Intune compliance (Log Analytics `IntuneDeviceComplianceOrg`), MDE `DeviceInfo`/`DeviceTvmSoftwareVulnerabilities` | Daily |
| R5 | **Autopilot & ESP health** | EP | Autopilot deployment report / Graph `autopilotEvents` (beta) | Daily |
| R6 | **Patch & feature update currency** | EP, SE, Steering | WUfB reports (`UCClient`, `UCClientUpdateStatus`), Autopatch reports | Daily |
| R7 | **App deployment health** (top failures, install success by ring) | EP, packaging | Intune app install status (Graph reports API) | Daily |
| R8 | **GPO migration burn-down** | EP, DA | GPA export + GPO link inventory | Weekly |
| R9 | **Endpoint experience** (Endpoint Analytics score, boot time, app reliability vs. baseline) | EP, Steering | Endpoint Analytics (Graph) | Weekly |
| R10 | **KFM / OneDrive health** | EP, Data | Remediation output + OneDrive sync admin reports | Daily during waves |
| R11 | **Encryption & LAPS** (escrowed %) | SE, Compliance | Intune encryption report, Entra BitLocker keys, LAPS | Weekly |
| R12 | **Token/connector expiry** | EP, ID | Graph (APNs, ADE, VPP, Managed Google Play, connectors) | Daily |
| R13 | **Exceptions register** (Path D, remediation scripts, CA/ASR exclusions) | DA, SE | Git + SharePoint list | Weekly |

### EE-6.2 Sample queries (adapt to your environment)

**Compliance by OS and ownership (Log Analytics: Intune diagnostic settings `IntuneDeviceComplianceOrg`)**

```kusto
IntuneDeviceComplianceOrg
| where TimeGenerated > ago(1d)
| summarize arg_max(TimeGenerated, *) by DeviceId
| summarize Devices = count(),
            Compliant = countif(ComplianceState == "Compliant")
          by OS, OwnerType
| extend CompliancePct = round(100.0 * Compliant / Devices, 1)
| order by Devices desc
```

**ASR rule audit hits to tune before block mode (Defender Advanced Hunting)**

```kusto
DeviceEvents
| where Timestamp > ago(14d)
| where ActionType startswith "Asr" and ActionType endswith "Audited"
| summarize Hits = count(), Devices = dcount(DeviceId) by ActionType, FileName, FolderPath
| order by Hits desc
```

**Quality-update currency (Windows Update for Business reports)**

```kusto
UCClient
| where TimeGenerated > ago(1d)
| summarize arg_max(TimeGenerated, *) by AzureADDeviceId
| summarize Devices = count(),
            Current = countif(OSSecurityUpdateStatus == "Latest")  // Latest | NotLatest | MultipleSecurityUpdatesMissing
          by OSVersion
| extend CurrentPct = round(100.0 * Current / Devices, 1)
```

> Column names and enumerations in the WUfB reports schema evolve. Validate against your workspace schema before you publish a dashboard. For Autopatch-managed rings, `OSSecurityUpdateComplianceStatus` (Compliant/NotCompliant/NotApplicable) measures compliance against the Autopatch deployment.

**Join-type distribution (Graph PowerShell)**

```powershell
Connect-MgGraph -Scopes "Device.Read.All"
Get-MgDevice -All -Property "id,displayName,trustType,operatingSystem,approximateLastSignInDateTime" |
  Where-Object { $_.OperatingSystem -eq "Windows" } |
  Group-Object TrustType |
  Select-Object @{n='JoinType';e={ switch ($_.Name) { 'AzureAd' {'Entra joined'} 'ServerAd' {'Hybrid joined'} 'Workplace' {'Registered'} default {$_} } }}, Count
```

**Co-managed vs. Intune-only Windows devices (Graph PowerShell)**

```powershell
Connect-MgGraph -Scopes "DeviceManagementManagedDevices.Read.All"
Get-MgDeviceManagementManagedDevice -All -Filter "operatingSystem eq 'Windows'" -Property "id,deviceName,managementAgent" |
  Group-Object ManagementAgent | Select-Object Name, Count
# configurationManagerClientMdm = co-managed; mdm = Intune only
```

## EE-7. KPIs you should own and track

| KPI | Definition | Target | Source | Frequency |
|---|---|---|---|---|
| **Intune enrollment coverage** | In-scope endpoints enrolled ÷ inventory | ≥ 99 % | Intune vs. MDE/CMDB reconciliation | Weekly |
| **Entra-joined share (Windows)** | Entra joined ÷ Windows fleet | 60 % M12 · 85 % M14 · 100 % excl. exceptions M18 | Entra | Weekly |
| **Co-management workloads on Intune** | Workloads × devices on Intune ÷ total | 100 % by M13 | MECM/Intune | Weekly |
| **Autopilot first-attempt success** | Successful deployments ÷ attempts | ≥ 95 % → 98 % | Autopilot report | Daily during waves |
| **Median provisioning time** | Power-on to desktop | ≤ 45 min (user-driven) / ≤ 15 min user phase (pre-provisioned) | Autopilot report | Weekly |
| **Device compliance** | Compliant ÷ managed | ≥ 97 % | Intune | Daily |
| **Quality update currency** | Devices on latest LCU within 14 days | ≥ 95 % | WUfB reports | Weekly |
| **Feature update currency** | Devices on target Windows 11 version | ≥ 95 % within 90 days of release | WUfB reports | Monthly |
| **App install success** | Required app installs succeeded ÷ attempted | ≥ 97 % | Intune | Daily |
| **GPOs remaining on workstation OUs** | Count | → 0 (≤ 25 Path D) | GPO inventory | Weekly |
| **Remediation-script debt** | Scripts replacing GPO settings | ≤ 30 with owners | Git | Monthly |
| **BitLocker escrow coverage** | Devices with recovery key in Entra ÷ encrypted | ≥ 99.5 % | Graph | Weekly |
| **Endpoint Analytics score** | EA overall | ≥ 75 | EA | Monthly |
| **Incidents per migrated device (10 days)** | Wave incidents ÷ devices | ≤ 3 % | ITSM | Per wave |
| **Endpoint-caused P1/P2 incidents** | Count | 0 per month after M12 | ITSM | Monthly |
| **Config drift items** | Unapproved differences prod vs. Git | 0 open > 5 days | M365DSC / IntuneManagement compare | Weekly |
