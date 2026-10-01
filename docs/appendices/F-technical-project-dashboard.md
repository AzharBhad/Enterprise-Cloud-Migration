# Appendix F: Technical Project Dashboard (Layout + Metrics)

> Audience: Design Authority, workstream leads, engineers, Service Desk leads. Format: **Power BI** (multi-page) + **Azure Workbooks** for live operational views. Refresh: hourly (wave days), daily otherwise.

## F.1 Page structure

| Page | Purpose | Key visuals |
|---|---|---|
| 1. Overview | Program technical health at a glance | KPI cards, trends, alerts |
| 2. Device migration | Path A/B/C/D progress by wave/site | Funnel, map by site, wave tracker table |
| 3. Co-management | Workload authority by collection | Matrix: workload × wave |
| 4. Autopilot & ESP | Provisioning health | Success rate trend, failure reasons, duration histogram |
| 5. Compliance & security | Compliance, encryption, LAPS, MDE, ASR | Stacked bars by OS/ring, non-compliance reasons |
| 6. Updates | Quality/feature/driver currency, Autopatch | Ring progression, devices by status |
| 7. Apps | Install success, failures, readiness | Top failing apps, readiness by wave |
| 8. Identity | Auth methods, CA impact, staged rollout | Method mix, CA failures, sign-ins by auth type |
| 9. Data & print | KFM, share migration, Universal Print | TB migrated, KFM %, print jobs |
| 10. GPO & config | GPO burn-down, drift, remediation debt | Burn-down line, drift list |
| 11. Service | Incidents by category/wave, MTTR | Heat map by wave/category |

## F.2 Page 1 layout (Overview)

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│ TECHNICAL DASHBOARD — updated 2027-06-14 09:00     Active wave: W3 (Clinics A)   │
├───────────┬───────────┬───────────┬───────────┬───────────┬───────────┬──────────┤
│ Intune    │ Entra     │ Compliant │ Autopilot │ Patch     │ App inst. │ Wi-Fi    │
│ enrolled  │ joined    │           │ success   │ currency  │ success   │ cert auth│
│ 98.7%     │ 41.2%     │ 96.1%     │ 96.4%     │ 93.0%     │ 97.8%     │ 99.7%    │
│ ▲0.3      │ ▲3.1      │ ▼0.4 ⚠    │ ▲0.8      │ ▲1.2      │ ─         │ ─        │
├───────────┴───────────┴───────────┴───────────┴───────────┴───────────┴──────────┤
│ ALERTS: ⚠ Compliance drop in Ring 3: BitLocker escrow missing on 112 devices      │
│         ⚠ APNs certificate expires in 41 days                                     │
├──────────────────────────────────────┬───────────────────────────────────────────┤
│ Join type over time (stacked area)   │ Co-mgmt workloads (matrix wave × workload)│
│ AD-only / Hybrid / Entra joined      │ ✓ Intune  ◐ Pilot  ✗ ConfigMgr            │
├──────────────────────────────────────┼───────────────────────────────────────────┤
│ Top 10 Autopilot/ESP failure reasons │ Incidents per migrated device by wave     │
└──────────────────────────────────────┴───────────────────────────────────────────┘
```

## F.3 Metric catalog

| # | Metric | Definition | Source | Target | Alert threshold |
|---|---|---|---|---|---|
| T1 | Intune enrollment coverage | Enrolled ÷ reconciled inventory (Intune ∪ MDE ∪ CMDB) | Graph + MDE `DeviceInfo` | ≥ 99 % | < 97 % |
| T2 | Join type distribution | Entra joined / Hybrid / AD-only / Registered | Graph `devices.trustType` | Plan curve | Behind plan by > 5 pts |
| T3 | Management authority | `managementAgent`: mdm vs. configurationManagerClientMdm vs. configurationManagerClient | Graph `managedDevices` | Intune-only rising | — |
| T4 | Co-mgmt workload authority | Per workload per collection | MECM co-mgmt dashboard / SQL | Per §18 | Mismatch with plan |
| T5 | Autopilot success (first attempt) | Successful ÷ total deployments | Autopilot deployment report | ≥ 95 % | < 90 % per day |
| T6 | Provisioning duration | P50/P90 OOBE → desktop | Autopilot report | P50 ≤ 45 min | P90 > 90 min |
| T7 | ESP failure reasons | Top N by app/policy/timeout | Autopilot/ESP report | — | New reason > 10 devices |
| T8 | Compliance % | By OS, ring, persona | `IntuneDeviceComplianceOrg` | ≥ 97 % | −2 pts day-over-day |
| T9 | Non-compliance reasons | Top settings failing | Intune Settings compliance report | — | — |
| T10 | BitLocker escrow coverage | Encrypted with key in Entra ÷ encrypted | Graph `informationProtection/bitlocker/recoveryKeys` + Intune encryption report | ≥ 99.5 % | < 98 % |
| T11 | LAPS backup coverage | Devices with LAPS password backed up recently | Graph `deviceLocalCredentials` | ≥ 99 % | < 97 % |
| T12 | MDE health | Active sensor, AV mode, tamper protection | Defender `DeviceInfo`/`DeviceTvmSecureConfigurationAssessment` | ≥ 99.5 % active | Inactive > 1 % |
| T13 | ASR audit/block events | By rule, ring | Advanced Hunting `DeviceEvents` | Trend down | Spike > 3× baseline |
| T14 | Quality update currency | Latest security update ÷ devices | `UCClient.OSSecurityUpdateStatus` | ≥ 95 % (14 d) | < 90 % |
| T15 | Feature update currency | Target Win11 version ÷ devices | `UCClient.OSVersion` | ≥ 95 % (90 d) | — |
| T16 | Autopatch release health | Paused/failed releases | Autopatch reports | 0 | Any |
| T17 | App install success | Succeeded ÷ attempted, required apps | Graph reports (app install status) | ≥ 97 % | App < 90 % |
| T18 | Wave app readiness | Ready apps ÷ wave app set (weighted) | App register | ≥ 98 % at T-2w | < 95 % |
| T19 | Auth method mix | WHfB / passkey / FIDO2 / CBA / Authenticator / SMS sign-ins | Entra sign-in logs (`AuthenticationDetails`) | Phishing-resistant ↑ | SMS share not declining |
| T20 | CA failures | Sign-ins failed due to CA by policy | Entra sign-in logs | ≤ baseline | > 1.5× baseline |
| T21 | Staged rollout / federation | Sign-ins by auth type (federated vs. managed) | Sign-in logs | Federated → 0 | — |
| T22 | KFM protected | KFM state per device | Remediation output | 100 % M12 | Wave < 98 % at T-1w |
| T23 | Share migration | TB / files migrated, errors | Migration Manager | Plan | Error rate > 1 % |
| T24 | Universal Print | Jobs succeeded/failed, printers online | Universal Print reports | ≥ 98 % success | — |
| T25 | GPO burn-down | GPOs linked to workstation OUs | AD/GPO inventory | → 0 | — |
| T26 | Config drift | Unapproved prod vs. Git differences | M365DSC / IntuneManagement compare | 0 | Any > 5 days |
| T27 | Token/cert expiry | Days to expiry (APNs, ADE, VPP, MGP, connectors, CMG cert) | Graph / connectors | > 60 days | ≤ 30 days |
| T28 | Incidents per migrated device | By wave, category | ITSM | ≤ 3 % | > 5 % |
| T29 | Endpoint Analytics score | Overall, startup, app reliability | EA | ≥ 75 | Regression > 5 pts |
| T30 | Remediation debt | Count of GPO-replacement scripts | Git | ≤ 30 | > 40 |

## F.4 Implementation notes

- Use **Azure Workbooks** on the Log Analytics workspace for live engineering views (no export delay). Use **Power BI** for cross-source joins (wave tracker, ITSM, plan).
- Store the device-level wave tracker in **Dataverse or SharePoint** with the Entra device ID as the key. Avoid hostnames, which change with Autopilot.
- **PHI note:** dashboards contain device and user names, so restrict them to the program/IT audience, which is the same sensitivity as the Intune console.
