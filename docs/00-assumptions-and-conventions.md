# 00 — Baseline Assumptions, Conventions, and Reading Guide

> **Status:** Draft v0.1 · **Owner:** Endpoint Engineering · **Audience:** Steering Committee, Program Team, Workstream Leads
>
> Every section of this blueprint is built on the baseline below. Where your real numbers differ, update this file first. Each assumption has an ID (`ASM-xx`) that later sections cite, so you can trace what changes when an assumption changes.

---

## 1. How to read this blueprint

| Marker | Meaning |
|---|---|
| **[E5]** | Needs Microsoft 365 E5, or the matching add-on (E5 Security, E5 Compliance, Entra ID P2). Not available on E3 alone. |
| **[E3+]** | Available on Microsoft 365 E3 as of the July 2026 Intune packaging change. Before that change it needed the Intune Suite or Plan 2. |
| **[ADD-ON]** | Needs a separately purchased SKU, such as Windows 365, Azure consumption, Intune Suite for non-E3/E5 users, or Defender for Identity on non-E5 users. |
| **[ASSUMPTION ASM-xx]** | The recommendation depends on an assumption you haven't confirmed. Validate it before you commit. |
| **[DECISION D-xx]** | A formal decision is needed. It is recorded in the Decision Log (see Governance, §31). |
| **[MS-BP]** | Clear Microsoft best practice. The blueprint recommends it without hedging. |
| **[OPINION]** | Architect's judgment where Microsoft guidance is neutral or reasonable people disagree. |

**Numbering:** Sections 1–33 follow the deliverables list. The Risk Analysis uses `R-xxx` IDs, the Endpoint Engineer role section uses `EE-x`, and the Appendices use letters (`App-A` to `App-L`).

**Timeline convention:** **M1 = October 2026.** The 18-month program runs from **M1 (Oct 2026) to M18 (Mar 2028)**. Optional Phase 6 (AD retirement) runs **M19–M30**.

---

## 2. Organization baseline (assumed)

### 2.1 Industry and regulation — `ASM-01`

| Item | Assumption |
|---|---|
| Industry | Regional **healthcare provider network**: 4 hospitals, 42 ambulatory clinics, 3 administrative campuses. |
| Primary regulation | **HIPAA** Privacy, Security, and Breach Notification Rules; **HITECH**. |
| Secondary | **HITRUST CSF** certification (r2) is held and must stay valid. **PCI DSS v4.0.1** applies to patient payment kiosks and registration desks. **21 CFR Part 11** applies to the laboratory and research systems that hold electronic records and signatures. State privacy and breach laws also apply. |
| FDA medical devices | Infusion pumps, imaging modalities, and similar devices are **out of scope for Intune management**. They are **in scope as dependencies**, because they use AD accounts, SMB shares, DNS, and DHCP. |
| Data residency | US only. Tenant data location: United States. |

### 2.2 Identities — `ASM-02`

| Population | Count | Notes |
|---|---|---|
| Employees (knowledge workers) | 7,300 | Admin, finance, IT, physicians' office staff. |
| Clinical frontline (shared-device heavy) | 1,900 | Nurses, techs, registration staff. Mostly on shared workstations, using tap-badge SSO (e.g., Imprivata OneSign). |
| Contractors / affiliates | 1,100 | Includes affiliated physicians on non-corporate devices. |
| **Total licensed humans** | **10,300** | |
| Generic / role accounts | ~520 | Nursing station logons, kiosks, scanners. Each must be classified (see §15). |
| Service accounts | ~1,350 | Only ~30 % are gMSA. The rest have non-expiring passwords. |
| Privileged accounts | ~410 | Domain Admins: 38. This is a known finding. |

### 2.3 Device estate — `ASM-03`

| Platform | Count | Current management | Notes |
|---|---|---|---|
| Windows — knowledge worker laptops/desktops | 8,900 | MECM, on-prem AD joined, ~2,100 already Hybrid Entra joined | 82 % Windows 11 (23H2/24H2), 18 % Windows 10 22H2 |
| Windows — shared clinical workstations | 1,800 | MECM, AD joined, Imprivata | Fast user switching, badge tap, kiosk-like lockdown through GPO |
| Windows — kiosks, signage, patient check-in | 450 | MECM, AD joined, AutoAdminLogon | PCI scope (check-in/payment) |
| Windows — VDI (non-persistent) | 650 | Citrix Virtual Apps and Desktops on-prem (vSphere) | Clinical EHR access; MCS-provisioned, AD joined |
| macOS | 650 | Jamf Pro (on-prem) | Research, marketing, executives |
| Linux (Ubuntu 22.04/24.04, RHEL 9) | 180 | Unmanaged / Ansible | Research and developers |
| iOS / iPadOS — corporate | 2,400 | Intune (MDM) | Includes 1,200 **shared clinical iPhones** (secure messaging, barcode scanning) |
| iOS / Android — BYOD | 2,400 | Intune MAM (partial) | Outlook and Teams only |
| Android Enterprise — dedicated (Zebra) | 900 | Intune, legacy device administrator on ~300 | Device administrator must be eliminated |
| **Total managed endpoints in scope** | **~18,330** | | |

> **Windows 10 note (urgent):** Windows 10 reached end of support on **14 October 2025**. Year 1 of Extended Security Updates (ESU) covers updates **up to 13 October 2026**, which is **12 days after M1**. **[ASSUMPTION ASM-03a]** The ~1,600 remaining Windows 10 devices are enrolled in ESU Year 1. You must buy ESU Year 2 for any device that won't be replaced or upgraded by mid-October 2026. This is risk **R-001** in the Risk Register.

### 2.4 Infrastructure baseline — `ASM-04`

| Component | Assumption |
|---|---|
| AD DS | Single forest, single domain (`corp.contoso-health.org`). Forest/domain functional level 2016. **24 DCs** across 2 datacenters and 10 larger sites. FSMO in the primary datacenter. |
| GPOs | **~1,150 GPOs**. ~35 % are unlinked or empty. 60+ use WMI filters. Heavy use of loopback processing on shared devices. |
| DNS | AD-integrated DNS on DCs. Conditional forwarders exist for partners. |
| DHCP | 6 Windows DHCP servers (failover pairs), ~900 scopes. DHCP options 66/67 used for PXE. |
| File services | 22 Windows file servers behind **DFS Namespaces**, ~**180 TB**. Home drives (H:) with **Folder Redirection** (Documents, Desktop). ~40 % of data not touched in more than 3 years. |
| Print | 14 Windows print servers, ~3,200 queues, ~1,600 MFPs. Scan-to-SMB and scan-to-email (SMTP relay). Badge release through a third-party product (e.g., PaperCut). |
| PKI | Two-tier AD CS: offline root plus 2 enterprise issuing CAs. Autoenrollment for user and machine certificates (Wi-Fi 802.1X EAP-TLS through Cisco ISE, VPN, S/MIME for ~200 users). **No NDES/SCEP deployed.** |
| Remote access | Cisco Secure Client (AnyConnect) VPN, device-certificate auth, split tunnel off (full tunnel). Legacy **DirectAccess** remains on ~300 devices. |
| Authentication | **AD FS 2019 farm** (4 nodes plus 2 WAP) federating the primary domain. **Password Hash Sync (PHS) enabled as backup.** Seamless SSO not configured. |
| Hybrid identity | **Microsoft Entra Connect Sync**, one active server plus one staging server. **[ASSUMPTION ASM-04a]** Version ≥ **2.5.79.0**, the minimum Microsoft required by **30 Sep 2026**. If not, synchronization stopped as of yesterday. **Verify on day 1.** |
| MECM | Configuration Manager current branch **2503**. One primary site (no CAS), 38 DPs, 1 SUP (WSUS), PXE-enabled DPs. **No CMG, no co-management, no tenant attach.** About 1,400 applications and packages, ~600 task sequences and steps sprawled across OSD. |
| Intune tenant | Exists. Used **only for iOS/Android** MDM and partial MAM. No Windows enrollment. No Autopilot. |
| Defender | **Microsoft Defender for Endpoint P1** (included in E3) is deployed through MECM. A legacy third-party EDR is still on ~25 % of devices, with its contract ending M9. |
| SIEM | Third-party SIEM on-prem. **Microsoft Sentinel not deployed.** |
| Azure | One Azure subscription (dev/test). No landing zone. One 1 Gbps ExpressRoute circuit to East US 2 (underused). |
| Network | Hub-and-spoke MPLS with local internet breakout at 4 hospitals only. Clinics backhaul to the datacenter. |

### 2.5 Licensing today — `ASM-05`

| SKU | Quantity | Covers |
|---|---|---|
| Microsoft 365 E3 | 8,400 | Knowledge workers, contractors who need full desktops |
| Microsoft 365 F3 | 1,900 | Clinical frontline |
| Windows 10/11 Enterprise E3 (VDA rights) | Included in M365 E3/F3 | VDI rights for licensed users |
| Defender for Endpoint P1 | Included in E3 | |
| Entra ID P1 | Included in E3/F3 | **No P2, so no PIM, ID Protection risk policies, or access reviews** |
| ConfigMgr | Through E3 (ConfigMgr rights included) | |

> The **target** licensing recommendation is in §29. In short, the blueprint is designed to **work on E3**, with clearly marked **[E5]** uplifts. The recommended target is **M365 E5 for knowledge workers and IT/privileged users, with F3 plus selected add-ons for frontline staff** **[DECISION D-01]**.

### 2.6 Sites and regions — `ASM-06`

| Site type | Count | WAN | Local infra today |
|---|---|---|---|
| Primary datacenter | 1 | 10 Gbps | DCs, MECM primary, file, print, AD FS, CAs |
| DR datacenter | 1 | 10 Gbps | DCs, file replicas, AD FS nodes |
| Hospitals | 4 | 1–2 Gbps, local breakout | DCs, DPs, file, print |
| Clinics | 42 | 50–200 Mbps, **no local breakout** | DP on 30 clinics, a local print server on 8 |
| Admin campuses | 3 | 500 Mbps | DCs, DPs |
| **Azure regions (target)** | **East US 2 (primary), Central US (secondary)** | | |

### 2.7 Timeline — `ASM-07`

- **Program duration:** 18 months to reach **cloud-first with cloud-native endpoints**: all endpoints Entra joined and Intune managed, MECM decommissioned, file and print modernized, AD reduced to a minimal Azure-hosted footprint.
- **Cloud-only identity** (AD DS fully retired): **M19–M30, gated on a business case**. **[OPINION]** For a healthcare network with FDA-regulated devices, an on-prem EHR, and Kerberos-bound clinical systems, committing leadership to "zero domain controllers in 18 months" would be dishonest. The blueprint commits to an aggressive, credible reduction instead, and makes the final retirement a measured decision.

---

## 3. Program roles (used in all RACI matrices)

| Role | Abbrev. | Description |
|---|---|---|
| Executive Sponsor | **ES** | CIO (or CIO and CMIO jointly for clinical scope). Owns the outcome and funding. Chairs the Steering Committee. |
| Program Manager | **PM** | Owns the integrated plan, RAID, budget tracking, and reporting. |
| Architect | **AR** | Cloud transformation and identity architect. Owns design authority. |
| Identity Team | **ID** | AD, Entra ID, AD FS, sync, PKI identity aspects. |
| Endpoint Team | **EP** | Intune, MECM, Autopilot, packaging, OS servicing. *(Your team.)* |
| Security Team | **SE** | SOC, Defender, Sentinel, CA design review, compliance evidence. |
| App Owners | **AO** | Business and clinical application owners, including vendors through them. |
| Service Desk | **SD** | Tier 1/2 support, floor-walkers, knowledge base. |

RACI legend: **R** = Responsible (does the work), **A** = Accountable (one per row, signs off), **C** = Consulted, **I** = Informed.

---

## 4. Confirmation tracker

Bring this table to the first Steering Committee meeting. Any assumption still unconfirmed at M2 becomes an entry in the RAID log.

| ID | Assumption | Confirm with | Due | Status |
|---|---|---|---|---|
| ASM-01 | Healthcare, HIPAA/HITRUST/PCI/Part 11 | Compliance Officer | M1 | ☐ |
| ASM-02 | Identity counts and service-account sprawl | Identity Team (AD export) | M1 | ☐ |
| ASM-03 | Device counts by platform | MECM + Intune + Jamf reports | M1 | ☐ |
| ASM-03a | Windows 10 ESU Year 1 coverage, Year 2 need | Endpoint + Procurement | **Week 1** | ☐ |
| ASM-04 | Infrastructure baseline | Infrastructure leads | M1 | ☐ |
| ASM-04a | Entra Connect version ≥ 2.5.79.0 | Identity Team | **Day 1** | ☐ |
| ASM-05 | Licensing inventory | Licensing / Procurement | M1 | ☐ |
| ASM-06 | Site inventory and WAN | Network Team | M1 | ☐ |
| ASM-07 | 18-month program with gated AD retirement | Executive Sponsor | M1 | ☐ |
