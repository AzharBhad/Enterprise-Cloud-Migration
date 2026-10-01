# 1. Executive Summary

> **Part A: Strategy** · Section 1 of 33 · Depends on: [00 Assumptions](../00-assumptions-and-conventions.md)

## 1.1 Purpose

This blueprint sets out how Contoso Health (10,300 users, ~18,300 endpoints, 49 sites) moves from a fully on-premises Microsoft estate to a **cloud-first** operating model in **18 months**. It also sets out a gated path to **cloud-only identity** afterwards. It is the single reference for leadership decisions, the integrated program plan, and the engineering work in each workstream.

## 1.2 The case for change

| Driver | What it means for us today | What changes |
|---|---|---|
| **Security and Zero Trust** | Identity is anchored in AD and AD FS. That is the #1 ransomware attack path in healthcare (Kerberoasting, Golden SAML, NTLM relay). There are 38 Domain Admins, and ~70 % of service accounts have non-expiring passwords. | Entra ID becomes the control plane. Conditional Access evaluates every sign-in against device compliance, risk, and location. Phishing-resistant MFA. PIM for privileged roles **[E5]**. |
| **Regulatory posture** | HIPAA Security Rule evidence (access control, audit, integrity, transmission security) is assembled by hand from on-prem logs. HITRUST r2 recertification is due during the program. | Policy-as-configuration in Intune and Entra gives continuous, exportable evidence. Sentinel plus Purview provide a unified audit trail **[E5 for full Purview Audit (Premium)]**. |
| **Clinician experience** | 18–25 minute PC rebuilds through PXE/OSD tie field techs to sites. VPN is needed for basic access. Shared workstations log in slowly (GPO processing, roaming profiles). | Autopilot + Intune: a device is ready from anywhere in under 60 minutes, with no tech on site. Windows Hello for Business and tap-badge on Entra-joined shared devices. No VPN for M365 or SaaS. |
| **Cost and complexity** | 24 DCs, 38 DPs, 22 file servers, 14 print servers, an AD FS farm, an MECM hierarchy, WSUS, and a two-tier PKI, all patched and backed up by hand. A datacenter hardware refresh is due M14. | Retire ~70 % of on-prem server roles. Avoid the hardware refresh. Shift from capex to an opex subscription model. |
| **Platform lifecycle** | Windows 10 ESU, ConfigMgr 2503 at end of support, AD FS hardening, and the Microsoft-led **Entra Connect Sync → Cloud Sync transition** are all forcing functions. | One modern servicing model: Windows Autopatch, Windows Update for Business, and Intune-native driver and firmware management. |

## 1.3 Target state, in one paragraph

Every Windows endpoint is **Microsoft Entra joined**, provisioned with **Windows Autopilot**, managed by **Microsoft Intune**, protected by **Defender for Endpoint**, and kept current by **Windows Autopatch**. Users authenticate with **Windows Hello for Business** or **FIDO2/passkeys**. Remaining on-prem Kerberos resources are reached through **cloud Kerberos trust** and **Microsoft Entra Private Access**. Every access decision passes through **Conditional Access**, which checks **Intune device compliance** and, with E5, **ID Protection risk**. User data lives in **OneDrive, SharePoint, and Teams**. Workloads that have to remain file-based use **Azure Files** with Entra Kerberos. Printing runs through **Universal Print**. Certificates come from **Microsoft Cloud PKI [E5]** or AD CS through the **Intune Certificate Connector**. Telemetry flows to **Microsoft Sentinel**. **MECM is decommissioned by M15.** AD DS shrinks to a **minimal, Azure-hosted, tier-0-hardened** footprint that exists only for the legacy systems that need it.

## 1.4 Approach

1. **Identity first, devices second, data third, infrastructure last.** You can't Entra-join a device until identity, authentication, and Conditional Access are ready. You can't retire file servers until users and apps have stopped mapping drives.
2. **Use co-management as a bridge.** Bring the existing ~11,000 Windows devices under Intune *without reimaging* (MECM co-management plus tenant attach). Move workloads in sequence. Then **convert devices to Entra join through Autopilot reset/re-provisioning, on the normal hardware refresh cycle or a planned "wipe-and-load" wave**. **[MS-BP]** Microsoft does not support in-place conversion from Hybrid join to Entra join.
3. **New devices are cloud-native from M5.** Every new or refreshed device ships as Entra joined through Autopilot. No new Hybrid-joined devices after M6 **[MS-BP]**.
4. **Ring-based rollout** with quantitative go/no-go gates (see §20 and §21). Clinical areas go last within each wave, and never during known high-acuity periods.
5. **Prove before you scale.** IT pilot (M4), business pilot (M6), clinical pilot (M8). Each pilot has exit criteria tied to KPIs.

## 1.5 Phases at a glance

| Phase | Months | Outcome |
|---|---|---|
| **0. Mobilize & Stabilize** | M1–M2 | Governance in place. Day-1 risks fixed (Entra Connect version, Win10 ESU, ConfigMgr upgrade). Full discovery started. |
| **1. Foundations** | M2–M5 | Azure landing zone, Entra hardening, CA baseline, Intune baseline, co-management enabled, Autopilot ready. |
| **2. Pilot** | M4–M8 | IT → business → clinical pilots. First co-management workloads moved. Cloud-native new devices. |
| **3. Scale** | M7–M14 | Production waves. All co-management workloads moved to Intune. Data migration to OneDrive/SharePoint. Universal Print. |
| **4. Decommission** | M12–M16 | MECM retired. AD FS retired. File and print servers retired. DC count reduced. |
| **5. Optimize & Handover** | M15–M18 | Cloud-only operating model. Autopatch steady state. Endpoint Analytics targets. Operational handover. |
| **6. AD Retirement (gated)** | M19–M30 | Business case for removing the remaining DCs. |

## 1.6 What we're asking leadership for

| # | Decision / commitment | Why now | Ref |
|---|---|---|---|
| D-01 | **Licensing uplift**: M365 E5 for knowledge workers and privileged users (or E3 + E5 Security). F3 + Entra ID P2 for frontline users who access PHI from shared devices. | PIM, ID Protection, Defender for Identity, Cloud PKI, EPM, and Sentinel benefits are core Zero Trust controls for a HIPAA covered entity. | §29 |
| D-02 | **Cloud-native by default**: no new Hybrid Entra joined devices after M6. | Each Hybrid device built now is technical debt we pay for later. | §10, §17 |
| D-03 | **Defederate AD FS by M10**, moving to managed authentication (PHS + Seamless SSO / PRT). | AD FS is a Tier-0 attack surface and a blocker for Cloud Sync. | §9 |
| D-04 | **Fund a hardware-refresh-aligned conversion**: ~3,000 devices/year are already scheduled for refresh, and these become Autopilot devices. The remaining in-life devices get an Autopilot-reset wave. | Avoids the cost of a dedicated reimage project. | §17, §20 |
| D-05 | **Data retention decision**: archive or delete file-share data not accessed in 3+ years (~70 TB) instead of migrating it. | Cuts migration effort by ~40 % and HIPAA exposure along with it. | §12 |
| D-06 | **Clinical change windows**: agree blackout calendars with nursing leadership and the CMIO. | Clinical safety and adoption. | §23 |
| D-07 | **Resourcing**: 6 FTE backfill, plus a Microsoft partner for packaging and data migration, plus a FastTrack engagement. | The current team runs BAU at 100 %. | §30 |

## 1.7 Benefits and measures of success

| Objective | KPI | Baseline | Target (M18) |
|---|---|---|---|
| Faster device provisioning | Median time from box to productive | ~2.5 days (ticket + tech + OSD) | **< 4 hours, zero-touch** |
| Security posture | Microsoft Secure Score | ~42 % (est.) | **≥ 75 %** |
| Device compliance | % of Intune-managed devices compliant | n/a | **≥ 97 %** |
| Patch currency | % of Windows devices on N or N-1 quality update within 14 days | ~78 % | **≥ 95 %** |
| Endpoint experience | Endpoint Analytics score | not measured | **≥ 75** |
| Phishing-resistant auth | % of users on WHfB / FIDO2 / passkey | ~0 % | **≥ 90 % (100 % privileged)** |
| Infrastructure reduction | On-prem servers supporting end-user computing | ~165 | **≤ 15** |
| Service desk | Tickets per 100 users per month | ~32 | **≤ 22** |

## 1.8 Top five risks (detail in §26)

| ID | Risk | Severity | Headline mitigation |
|---|---|---|---|
| R-001 | Windows 10 ESU Year 1 expires 13 Oct 2026 | **Critical** | Buy ESU Year 2 for the residual fleet in week 1. Accelerate Win11 upgrades through MECM, then Autopatch. |
| R-002 | Hidden Kerberos/NTLM/LDAP dependencies break clinical apps after Entra join | **Critical** | Defender for Identity + DC log analysis (§15). Cloud Kerberos trust. Entra Private Access. Clinical pilot gate. |
| R-003 | Shared clinical workstations (tap-badge SSO) are incompatible with Entra join | **High** | Vendor validation in M2–M3. Keep the shared-device ring Hybrid joined (co-managed) until the vendor's Entra-joined mode is certified. |
| R-004 | Data migration loses permissions or exposes PHI through oversharing | **High** | Permission mapping, sensitivity labels, SharePoint Advanced Management, and pre-migration access cleanup. |
| R-005 | Program fatigue and change saturation in clinical staff | **High** | Clinical champions, at-the-elbow support, blackout calendars, and fewer touches by combining changes in each wave. |

## 1.9 Bottom line

The migration is **achievable in 18 months for endpoints, collaboration, data, and print**, and should be committed to on that basis. **Full AD retirement should not be promised in the same window.** It should be earned through the dependency work in §15 and decided at M18 on evidence. The largest single success factor is the **quality of discovery in the first 90 days**: in healthcare migrations, dependencies are what turn a 3-month delay into a 12-month one.
