# 8. Infrastructure Prerequisites

> **Part A: Strategy** · Section 8 of 33 · Each prerequisite has a "needed by" month. Anything late moves the critical path (§6.4).

## 8.1 Licensing and tenant prerequisites

| # | Prerequisite | Needed by | Licensing dependency | Owner |
|---|---|---|---|---|
| PR-L1 | Intune license assigned to every user who will enroll a device (M365 E3/E5/F3 include Intune Plan 1) | M3 | E3/F3 | Licensing |
| PR-L2 | Entra ID P1 for all users (CA, dynamic groups, automatic MDM enrollment) | M2 | E3/F3 | Licensing |
| PR-L3 | **Entra ID P2** for privileged users at minimum (PIM, ID Protection) | M2 | **[E5]** or P2 add-on | Licensing |
| PR-L4 | **Defender for Endpoint P2** (EDR, AIR, TVM) | M4 | **[E5]** or E5 Security add-on | Licensing |
| PR-L5 | **Defender for Identity** | M1 | **[E5]** or add-on | Licensing |
| PR-L6 | Windows Enterprise E3/E5 (for Autopatch, WUfB driver mgmt, Win11 Enterprise features such as Credential Guard defaults) | M4 | E3/E5 | Licensing |
| PR-L7 | Cloud PKI, EPM, Enterprise App Management | M4 | **[E5]** (from July 2026 packaging) or Intune Suite add-on | Licensing |
| PR-L8 | Remote Help, Advanced Analytics, Intune Plan 2 | M5 | **[E3+]** from July 2026 packaging. **Confirm the tenant has been provisioned** (Microsoft rolls these out gradually, with a 30-day Message Center notice). | Licensing |
| PR-L9 | Entra Private Access (or Entra Suite) | M6 | **[ADD-ON]** | Licensing |
| PR-L10 | Windows 10 **ESU Year 2** for residual Win10 devices | **M1 (wk 1)** | **[ADD-ON]** | Procurement |
| PR-L11 | Azure subscription(s) under an Enterprise Agreement / MCA with budget alerts | M2 | Azure consumption | Cloud |
| PR-L12 | Universal Print (included in M365 E3/E5/F3 with a pooled monthly job allowance; validate the volume against ~1,600 MFPs) | M5 | E3/F3 + possible add-on volume | Licensing |
| PR-L13 | Test tenant (separate) with E5 trial/dev licenses for validation | M3 | Trial / purchase | EP |

## 8.2 Identity and directory prerequisites

| # | Prerequisite | Detail | Needed by | Owner |
|---|---|---|---|---|
| PR-I1 | **Entra Connect Sync at a supported version** | Minimum 2.5.79.0 by 30 Sep 2026, or synchronization fails. Target the current 2.6.x release, which includes a guided Cloud Sync migration workflow. Keep the staging server at the same version. | **M1 day 1** | ID |
| PR-I2 | Routable UPN = primary SMTP for all users | Fix the 4 % mismatches (CS-ID-04) | M3 | ID |
| PR-I3 | Sync scope cleaned | Exclude stale and service accounts that don't need cloud identity. **Device OUs in scope** for Hybrid join. | M3 | ID |
| PR-I4 | Hybrid Entra join configured | SCP in AD; device registration GPO/target OUs; validate `dsregcmd /status` | M3 | ID + EP |
| PR-I5 | Password Hash Sync enabled and healthy | Already on (backup). Confirm with Connect Health. | M2 | ID |
| PR-I6 | Seamless SSO (bridge for down-level / Hybrid devices) | `AZUREADSSOACC` computer account. **Roll the Kerberos decryption key every 30 days.** | M4 | ID |
| PR-I7 | **Cloud Kerberos trust** for WHfB | DCs on Windows Server 2016 or later, fully patched (see Microsoft's "WHfB cloud Kerberos trust" prerequisites for minimum updates). Create the `AzureADKerberos` read-only DC object per domain. | M4 | ID |
| PR-I8 | Authentication methods policy | Migrated off the legacy MFA/SSPR policies. Authenticator (number matching), FIDO2, passkeys, and Temporary Access Pass enabled. SMS/voice scheduled for removal. | M2 | ID |
| PR-I9 | Break-glass accounts | 2 cloud-only accounts, FIDO2, excluded from all CA policies, with sign-in alerts | M2 | ID |
| PR-I10 | Group strategy | Dynamic device groups (e.g., `device.devicePhysicalIds -any _ -eq "[OrderID]:…"` for Autopilot group tags); role-based user groups; naming convention | M3 | ID + EP |
| PR-I11 | Entra Connect: group writeback / device writeback decisions | Device writeback is only needed for AD FS-based scenarios. **Plan to remove it** after defederation. | M4 | ID |

## 8.3 Network prerequisites

| # | Prerequisite | Detail | Needed by | Owner |
|---|---|---|---|---|
| PR-N1 | **Intune, Autopilot, Windows Update, Delivery Optimization, MDE, Entra, M365 endpoints** allowed at all sites | Use Microsoft's published endpoint lists (Intune network endpoints; M365 URL/IP web service). **No TLS inspection** on Intune/Autopilot/WNS/MDE/Entra authentication endpoints. | M3 | Network |
| PR-N2 | Local internet breakout at the 42 clinics (SD-WAN) for M365 "Optimize/Allow" categories | Avoids hairpinning Autopilot/Autopatch traffic | M7 (before clinic waves) | Network |
| PR-N3 | Delivery Optimization | Allow TCP **7680** (peer-to-peer) on the LAN and UDP **3544** (Teredo, for internet peers if used). Set `DOGroupIdSource` / `DODownloadMode` = 1 or 2 by site subnet. | M4 | Network + EP |
| PR-N4 | **Microsoft Connected Cache** for Enterprise | 6–8 cache nodes at the hospitals and largest clinics (Windows or Linux host) | M7 | EP + Infra |
| PR-N5 | Proxy authentication bypass for SYSTEM context | The Intune Management Extension, Autopilot, and MDE run as SYSTEM, so authenticated proxies break them | M3 | Network |
| PR-N6 | Time sync | Entra-joined devices use `time.windows.com` or internal NTP; Kerberos needs ±5 min | M4 | Network |
| PR-N7 | DNS for cloud Kerberos | Entra-joined devices must resolve `_ldap._tcp.dc._msdcs.<domain>` and DCs (on-network, or through Private Access) | M4 | ID + Network |
| PR-N8 | 802.1X / Cisco ISE | ISE accepts certs from Cloud PKI / SCEP issuing CA. ISE authorization rules don't depend on an AD computer account lookup. Optional ISE ↔ Intune compliance integration (confirm ISE version support). | **M4** ⚠️ | Network |
| PR-N9 | Guest/onboarding SSID or wired onboarding VLAN | Autopilot OOBE needs internet before certs exist | M4 | Network |
| PR-N10 | ExpressRoute redundancy + Azure hub | Second circuit or VPN failover; hub firewall | M4 | Cloud |
| PR-N11 | Private Network Connectors (Entra Private Access / App Proxy) | ≥ 2 per connector group, in the datacenter and Azure, Windows Server 2019+ | M5 | Network + ID |

## 8.4 On-premises / IaaS component prerequisites

| # | Component | Requirement | Needed by | Owner |
|---|---|---|---|---|
| PR-C1 | **Configuration Manager** | Upgrade 2503 → **2609** (current baseline, support until Mar 2028). Note: the 2603 release notes warn that an internal compliance service is deprecated in **October 2026**, so co-managed environments with the Compliance workload on Intune must be on ≥ 2603. | M2 | EP |
| PR-C2 | **Cloud Management Gateway** | VM scale set-based CMG, Entra app registrations, server authentication cert (public or PKI), Entra (token-based) client auth | M2 | EP |
| PR-C3 | Co-management prerequisites | Devices Hybrid joined (for existing devices). Intune auto-enrollment user scope. MECM clients on a current version. **Co-management settings: "Automatic enrollment in Intune = Pilot" first.** | M3 | EP |
| PR-C4 | **Intune Certificate Connector** | Windows Server 2016+ (2022 recommended), 2 servers for HA. PKCS (S/MIME). SCEP through NDES only if Cloud PKI isn't licensed. | M4 | ID + EP |
| PR-C5 | NDES (only if no Cloud PKI) | Windows Server, NDES role, published through Entra App Proxy, dedicated service account (gMSA where supported) | M4 | ID |
| PR-C6 | **Universal Print connector** | 2 VMs for legacy printers without native UP support. Firmware updates on UP-ready MFPs. | M5 | EP |
| PR-C7 | Intune Connector for Active Directory | **Avoid**. Only needed for Hybrid Autopilot, which this blueprint doesn't use for new devices. If used temporarily, deploy the current version with a managed service account. | n/a | EP |
| PR-C8 | **Defender for Identity sensors** | On every DC, AD FS, AD CS, and Entra Connect server. Sensor v3.x where supported. | M2 | SE |
| PR-C9 | Domain controllers | All DCs on Windows Server 2019+ (2022/2025 preferred) by M8. DCs as Tier-0 assets only. | M8 | ID |
| PR-C10 | Azure IaaS DCs | 2 per region in the identity subscription of the landing zone, with Azure Backup and Defender for Servers | M10 | ID + Cloud |

## 8.5 Azure landing zone prerequisites

| # | Prerequisite | Detail | Needed by |
|---|---|---|---|
| PR-A1 | Management group hierarchy (CAF) | Platform (Identity, Management, Connectivity) + Landing Zones (Corp, Online) + Sandbox | M3 |
| PR-A2 | Identity subscription | Azure IaaS DCs, Private Network Connectors, Cert Connector, UP connector | M3 |
| PR-A3 | Management subscription | Log Analytics workspace(s), Sentinel, Automation, Azure Monitor | M3 |
| PR-A4 | Azure Policy baseline | Allowed regions (US), HIPAA/HITRUST regulatory compliance initiative assigned, diagnostic settings | M3 |
| PR-A5 | AVD/W365 network | Spoke VNet for AVD host pools / Azure Network Connection for W365 (if hybrid network needed) | M9 |
| PR-A6 | Storage for archive | Immutable Blob (time-based retention) for file-share archive and MECM DB archive | M10 |
| PR-A7 | BAA | **HIPAA Business Associate Agreement** with Microsoft is in place (it's part of the Microsoft Product Terms / DPA for in-scope services). Confirm every service used is in BAA scope. | **M1** |

## 8.6 Endpoint hardware and OS prerequisites

| # | Prerequisite | Detail | Needed by |
|---|---|---|---|
| PR-E1 | Windows 11 Enterprise 23H2+ (24H2/25H2 target) | Autopilot device preparation needs Win11 22H2/23H2 with KB5035942 or later | M5 |
| PR-E2 | TPM 2.0, UEFI, Secure Boot | Required for Win11, WHfB, BitLocker silent enable, Autopilot self-deploying/pre-provisioning (TPM attestation) | M5 |
| PR-E3 | Autopilot registration | OEM/reseller registration for new devices. Hardware hash capture (Intune "Convert all targeted devices to Autopilot", or a script for in-life Path B devices). | M4 |
| PR-E4 | Firmware/BIOS management | OEM tools (Dell Command / HP Image Assistant / Lenovo) through Intune, plus **Intune driver & firmware update policies** | M7 |
| PR-E5 | macOS 14+ for Platform SSO | Apple Business Manager linked to Intune (ADE token, VPP/Apps & Books) | M6 |
| PR-E6 | Apple Business Manager / Android Enterprise binding | ABM → Intune ADE. Managed Google Play binding. Zebra OEMConfig. | M5 |
| PR-E7 | Linux: Ubuntu 22.04/24.04 LTS, RHEL 8/9 (Intune-supported distros) | Microsoft Edge + Intune portal app for compliance; MDE for Linux | M9 |

## 8.7 Operational prerequisites

| # | Prerequisite | Needed by | Owner |
|---|---|---|---|
| PR-O1 | **Privileged Access Workstations** for Tier-0/cloud admins | M3 | SE + EP |
| PR-O2 | Intune RBAC model + scope tags + **multi-admin approval** (apps, scripts, device wipe) | M3 | EP |
| PR-O3 | Configuration-as-code repo (Git) + daily export (Microsoft365DSC / IntuneManagement / Graph) | M3 | EP |
| PR-O4 | ITSM integration (ServiceNow/other ↔ Intune/Graph) for device lifecycle and incident tagging (`CloudMigration` category) | M5 | SD |
| PR-O5 | Service Desk tooling: Remote Help, LAPS retrieval role, BitLocker key retrieval role, Autopilot reset rights | M5 | SD + EP |
| PR-O6 | Log Analytics: Intune diagnostic settings (AuditLogs, OperationalLogs, DeviceComplianceOrg, Devices), Entra sign-in/audit logs | M3 | SE + EP |
| PR-O7 | Microsoft FastTrack engagement (Intune, Entra, Defender, OneDrive/SharePoint migration guidance) | M2 | PM |

## 8.8 Prerequisite RACI

| Prerequisite group | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Licensing (8.1) | **A** | R | C | C | C | C | I | I |
| Identity (8.2) | I | I | C | **A/R** | C | C | I | I |
| Network (8.3) | I | C | **A** | C | C | C | I | I |
| On-prem components (8.4) | I | I | **A** | R | R | C | I | I |
| Azure landing zone (8.5) | I | C | **A** | C | C | R | I | I |
| Endpoint hardware/OS (8.6) | I | I | C | I | **A/R** | C | C | I |
| Operational (8.7) | I | R | C | C | **A** | R | I | R |

> Network and landing-zone rows assume the Architect is accountable and the Network/Cloud platform teams are responsible. Those teams aren't one of the eight standard program roles, so they're reflected under AR.

---

**End of Part A.** Next: **Part B: Workstream Plans, starting with §9 Identity Modernization Plan.**
