# 13. Security Transformation Plan

> **Part B: Workstream Plans** · Section 13 of 33 · Lead: Security Team · Related: §9 Identity, §10 Endpoint, §14 GPO, §16 Certificates, §28 Compliance

## 13.1 Zero Trust target model

| Pillar | Today (maturity) | Target (M18) | Key controls |
|---|---|---|---|
| **Identities** | Traditional (1) | Advanced (4) | Phishing-resistant MFA, CA with authentication strengths, ID Protection **[E5]**, PIM **[E5]**, MDI **[E5]** |
| **Endpoints** | Traditional (2) | Advanced (4) | Intune compliance as CA signal, MDE P2 risk score **[E5]**, ASR, LAPS, EPM **[E5]**, App Control for kiosks/PAW |
| **Applications** | Traditional (1) | Advanced (3–4) | All SaaS behind Entra SSO; Defender for Cloud Apps **[E5]**; legacy apps through Private Access with per-app CA |
| **Data** | Traditional (1) | Advanced (3) | Sensitivity labels, DLP (Exchange/SPO/OneDrive/Teams, Endpoint DLP **[E5]**), MAM for BYOD |
| **Infrastructure** | Traditional (2) | Advanced (3) | Tier-0 hardening of residual AD, Defender for Servers, Azure Policy |
| **Network** | Traditional (1) | Advanced (3) | Entra Private Access (ZTNA), Internet Access/SSE, 802.1X with Intune certs, microsegmentation of clinical networks |
| **Visibility & automation** | Traditional (1) | Advanced (4) | Defender XDR, Sentinel, automated playbooks (Logic Apps) |

## 13.2 Conditional Access: full policy set

Naming: `CA<nnn>-<Persona>-<App>-<Control>`. All policies are built **report-only first**, analyzed with the **CA insights and reporting workbook** (Log Analytics) for ≥ 14 days, piloted to rings, and then enforced. Exclusions are **groups** with owners and reviews, never individual users.

| ID | Persona | Target apps | Conditions | Grant / session | Licensing |
|---|---|---|---|---|---|
| CA001–CA008 | (Identity-owned; see §9.6) | | | | |
| CA100 | Internal users | Office 365 | Platforms: Windows, macOS, Linux | **Require compliant device** OR **Hybrid Entra joined** (Path D group only) | P1 |
| CA101 | Internal users | All resources | Platforms: iOS, Android; device filter = corporate | Require compliant device | P1 |
| CA102 | Internal users | Office 365 | Platforms: iOS, Android; personal | **Require app protection policy** (MAM) | P1 |
| CA103 | Internal users | Office 365 | Browser on unmanaged device | **App-enforced restrictions** (SPO/EXO web: no download) / Defender for Cloud Apps session control | P1 / **[E5]** for MDCA |
| CA104 | All users | All resources | Device platforms: unknown/unsupported | Block | P1 |
| CA105 | Shared clinical users | All resources | Device filter: `device.extensionAttribute2 -eq "SharedClinical"` | Compliant device + authentication strength "Clinical CBA/FIDO2"; **sign-in frequency** per clinical policy | P1 |
| CA106 | All users | Entra Private Access apps (per segment) | — | Compliant device + MFA; EHR segment requires phishing-resistant | Private Access **[ADD-ON]** |
| CA107 | Admins | Microsoft admin portals, Azure management | — | Phishing-resistant MFA + **compliant PAW** (device filter `PAW`) | P1 |
| CA108 | Guests | All | — | MFA, terms of use, session timeout 8 h | P1 |
| CA109 | All users | All | Locations: countries outside allowed list | Block (exceptions: travel group with expiry) | P1 |
| CA110 | All users | Intune enrollment | — | MFA (TAP allowed) | P1 |
| CA111 | Kiosk device identities | — | Kiosk autologon accounts excluded from user MFA; **restricted by device filter and named location** | Block anything outside kiosk app set | P1 |
| CA112 | All users | Azure Files storage app(s) | — | **Excluded from MFA policies** (Entra Kerberos limitation); compensating: compliant device + network-restricted | P1 |
| CA113 | High-risk PHI apps (EHR web, HR, finance) | Selected | — | **Authentication context** `c1` = phishing-resistant + compliant; step-up from SharePoint labels | P1 (auth context) |

## 13.3 Endpoint security transformation

### 13.3.1 Defender for Endpoint

| Step | Action | Month |
|---|---|---|
| 1 | License MDE P2 **[E5]** (or E5 Security add-on). Connect **MDE ↔ Intune** (service-to-service), enable **Security settings management** | M3 |
| 2 | Onboard through Intune **EDR policy** (co-managed: Endpoint Protection workload pilot; others through existing MECM onboarding) | M4–M8 |
| 3 | Run legacy EDR and MDE side-by-side: MDE AV in **passive mode** where legacy AV is present (`ForceDefenderPassiveMode`), **EDR in block mode** on | M4–M8 |
| 4 | Remove legacy EDR/AV per ring; MDE AV switches to **active** automatically when the third-party AV is uninstalled (on Windows client) | M6–M9 |
| 5 | Turn on **tamper protection** (tenant-wide), **network protection** (block), **web content filtering**, **automated investigation & remediation (full)** | M6 |
| 6 | **Defender Vulnerability Management**: weekly exposure review; feed remediation into Autopatch/app updates | M6+ |
| 7 | **Device discovery** to find unmanaged endpoints (feeds the inventory reconciliation KPI) | M5 |

### 13.3.2 Attack Surface Reduction

| Phase | Rules | Mode |
|---|---|---|
| M4–M6 | All standard ASR rules | **Audit** org-wide; collect `DeviceEvents` in Advanced Hunting |
| M6–M8 | "Standard protection" rules (block credential stealing from LSASS, block abuse of exploited vulnerable signed drivers, block persistence through WMI) | **Block** org-wide |
| M8–M12 | Office/script/email rules (block Office child processes, block obfuscated scripts, block executable content from email, block untrusted/unsigned USB processes, ransomware advanced protection) | **Block** per ring, with per-rule exclusions from audit data |
| Ongoing | Warn mode for the rules users can override, where a business need exists (e.g., research) | Warn |

### 13.3.3 Encryption: MBAM → Intune

| Step | Action |
|---|---|
| 1 | Intune **Disk encryption → BitLocker** policy: XTS-AES 256, OS drive TPM-only (silent), fixed drives required, **Save BitLocker recovery information to Entra ID = Required before enabling BitLocker** |
| 2 | For devices already encrypted: deploy the **BitLocker recovery key escrow** remediation script (`BackupToAAD-BitLockerKeyProtector`) and verify keys in Entra |
| 3 | Switch the Endpoint Protection workload (and Device configuration) to Intune; remove the MECM BitLocker management policy |
| 4 | **Recovery key rotation** after use (Intune: client-driven recovery password rotation) |
| 5 | Report: Intune *Encryption report* + Entra key presence report (Graph `informationProtection/bitlocker/recoveryKeys`) |
| 6 | Service Desk: self-service BitLocker key retrieval from the My Account portal (company policy permitting) + SD Tier 1 role with audit |

### 13.3.4 Local admin and privilege

| Control | Design | Licensing |
|---|---|---|
| **Windows LAPS** | Intune *Account protection → LAPS*: backup to Entra ID, 30-day rotation, password complexity 4 (large + small + numbers + specials), **post-authentication actions: reset password and log off** after 8 h; legacy LAPS GPO removed per ring | E3 |
| **Remove standing local admin** | Intune *Account protection → Local user group membership* (`Administrators` = Remove + Add [LAPS account, `Entra Joined Device Local Administrator` role holders]). **Don't add broad groups.** | E3 |
| **Endpoint Privilege Management** | Elevation settings: default response = *require support approval*; elevation rules for 120 known apps (file hash + publisher cert); user-confirmed elevation for physicians' dictation & research tools; reporting reviewed weekly | **[E5]** (from July 2026) |
| **App Control for Business** | Enforced on kiosks, PAWs, PCI devices (managed installer = Intune Management Extension); audit on others | E3 |

### 13.3.5 Remote access and network

| Control | Design |
|---|---|
| **Entra Private Access** | Global Secure Access client deployed through Intune (Win32); **Quick Access** for broad on-prem ranges initially, then **per-app Enterprise Applications** (EHR, PACS viewers, file servers, intranet). Per-app CA (CA106). **Kerberos SSO** through private DNS + DC segments. Connectors: 2× per connector group (datacenter, Azure). |
| **Entra Internet Access** | Optional SSE replacement for web proxy ([ADD-ON] Entra Suite). Otherwise keep the existing SWG with M365 traffic bypass. |
| **VPN retirement** | DirectAccess off M6. Cisco VPN restricted to the exceptions group by M14 (protocols not supported by Private Access, e.g., certain UDP medical-device admin tools). |
| **802.1X** | ISE with Intune-issued certs (§16); unmanaged/non-compliant → remediation VLAN |

## 13.4 Data protection

| Control | Scope | Licensing |
|---|---|---|
| Sensitivity labels: Public / Internal / Confidential / **Confidential – PHI** / Highly Confidential | All M365 workloads; container labels for sites/teams | E3 (manual), **[E5]** auto-labeling |
| DLP: HIPAA (US) template, US SSN/DEA/NPI SITs, custom MRN SIT | Exchange, SharePoint, OneDrive, Teams | E3 (core), **[E5]** Teams chat & Endpoint DLP |
| **Endpoint DLP** | Block copy of PHI-labeled files to USB/personal cloud/unallowed apps | **[E5]** |
| App Protection Policies | iOS/Android Level 2/3; Windows MAM for Edge (BYOD browser) | P1 / E3 |
| Defender for Cloud Apps | Shadow IT discovery (through MDE), session controls, OAuth app governance | **[E5]** |
| Audit | Purview Audit (Standard 180 days); **Audit (Premium) 1-year+ retention, intelligent insights** | **[E5]** for Premium |

## 13.5 Privileged access (PAM/PIM) and tiering

| Tier | Assets | Accounts | Access path |
|---|---|---|---|
| **Tier 0 (cloud)** | Entra tenant, Intune tenant admin, Azure root MG | Cloud-only `adm-` accounts, PIM-eligible, FIDO2 | PAW + CA107 |
| **Tier 0 (on-prem)** | DCs, AD CS, Entra Connect, MDI sensors, PAWs | `t0-` accounts (Protected Users group), smart card/FIDO2 where possible | PAW only; DCs accept logons only from PAWs (authentication policies/silos) |
| **Tier 1** | Servers, apps, Azure subscriptions | `t1-` / cloud `adm-` with PIM for Azure roles | PAW or jump host (Azure Bastion) |
| **Tier 2** | Endpoints | Service Desk roles in Intune, LAPS retrieval | Standard workstation with CA |

PIM settings **[E5]**: max activation 4 h (8 h for Intune admins during waves), justification + ticket number, **approval** for Global Administrator, Privileged Role Administrator, Conditional Access Administrator, Intune Administrator; **PIM for Groups** for Azure RBAC and on-prem-synced admin groups (where still required).

## 13.6 Security operations

| Element | Design |
|---|---|
| SIEM | **Microsoft Sentinel** on the management subscription's Log Analytics workspace; connectors: Defender XDR (incidents + raw), Entra ID (sign-in, audit, risk, provisioning), Intune (diagnostic settings), Azure Activity, Windows Security Events through AMA (DCs, Tier-0), firewall/ISE syslog |
| Cost control | Analytics tier for detection-relevant tables; **data lake / Basic/Auxiliary tiers** for high-volume logs; E5 data grant for Defender/Entra data where applicable |
| Detections | Microsoft analytics rules + custom: break-glass sign-in, CA policy change, Intune wipe in bulk, LAPS mass retrieval, PIM activation outside change window, new federated domain, Entra Connect configuration change |
| Automation | Logic Apps playbooks: isolate device on high-severity alert (with clinical-device exclusion list), revoke sessions on user risk high, ticket creation in ITSM |
| Overlap | Legacy SIEM receives forwarded events until M9; Sentinel primary from M8 |

## 13.7 Secure Score roadmap

| Milestone | Target Secure Score | Driven by |
|---|---|---|
| M1 baseline | ~42 % | — |
| M4 | 55 % | Legacy auth blocked, MFA everywhere, admin MFA strength, ASR audit, tamper protection |
| M9 | 65 % | Compliance-based CA, ASR block, LAPS, BitLocker 98 %, PIM |
| M14 | 72 % | Device-based CA for all, DLP, Defender for Cloud Apps, legacy protocols off (NTLMv1, SMBv1) |
| M18 | ≥ 75 % | Steady state + exceptions governed |

## 13.8 RACI: Security transformation

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Zero Trust target model | C | I | R | C | C | **A** | I | I |
| Conditional Access design & change | I | I | C | R | C | **A** | C | I |
| MDE rollout & legacy EDR removal | I | C | C | I | R | **A** | I | C |
| ASR rules | I | I | C | I | R | **A** | C | C |
| BitLocker MBAM → Intune | I | I | C | I | **A/R** | C | I | C |
| LAPS / EPM | I | I | C | I | R | **A** | C | C |
| Private Access & VPN retirement | I | C | **A** | C | R | R | C | C |
| Labels & DLP | I | I | C | I | C | **A/R** | C | I |
| PIM & tiering | C | I | C | R | C | **A** | I | I |
| Sentinel & SOC processes | I | I | C | C | C | **A/R** | I | I |

## 13.9 Exit criteria

- [ ] All CA policies in §9.6 and §13.2 enforced. Exclusions governed and reviewed quarterly.
- [ ] MDE P2 active on ≥ 99.5 % of endpoints. Legacy EDR removed.
- [ ] ASR block mode org-wide for the standard protection set.
- [ ] No standing local admin outside LAPS/EPM. Legacy LAPS GPO removed.
- [ ] Sentinel is the primary SIEM. Legacy SIEM decommissioned.
- [ ] Secure Score ≥ 75 %.
