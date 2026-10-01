# 7. Detailed Month-by-Month Timeline

> **Part A: Strategy** · Section 7 of 33 · M1 = October 2026 · ⭐ = milestone · 🚦 = gate · ⚠️ = critical-path item

## 7.1 Summary view

| Month | Calendar | Phase | Headline | Milestones / gates |
|---|---|---|---|---|
| M1 | Oct 2026 | 0 | Mobilize; fix day-1 risks | ⭐ Program kickoff · ⭐ ESU Y2 purchased · ⭐ Entra Connect verified |
| M2 | Nov 2026 | 0 → 1 | Discovery at full speed; foundations start | 🚦 Phase 0 gate · ⭐ ConfigMgr 2609 |
| M3 | Dec 2026 | 1 | Co-management live; CA report-only | ⭐ Co-management enabled · ⭐ Tenant attach 100 % |
| M4 | Jan 2027 | 1 | Autopilot + certs proven; WHfB CKT | ⭐ First Entra-joined device on corporate Wi-Fi ⚠️ |
| M5 | Feb 2027 | 1 → 2 | IT pilot | 🚦 Foundations gate · ⭐ IT pilot start |
| M6 | Mar 2027 | 2 | Business pilot; new devices cloud-native | ⭐ No new Hybrid devices (D-02) · ⭐ DirectAccess retired · 🚦 IT pilot exit |
| M7 | Apr 2027 | 2 | Clinical pilot; workloads moving | ⭐ Clinical pilot start · 🚦 Business pilot exit |
| M8 | May 2027 | 2 → 3 | Production waves begin | 🚦 Clinical pilot exit ⚠️ · ⭐ Wave 1 |
| M9 | Jun 2027 | 3 | Scale; legacy EDR retired | ⭐ Legacy EDR off · 🚦 D-09 MECM retention decision |
| M10 | Jul 2027 | 3 | Defederation | ⭐ Domain converted to managed auth ⚠️ |
| M11 | Aug 2027 | 3 | Scale; AD FS gone | ⭐ AD FS decommissioned |
| M12 | Sep 2027 | 3 → 4 | Halfway through waves; shared clinical starts | ⭐ 60 % Entra joined · 🚦 Mid-program review |
| M13 | Oct 2027 | 3/4 | All workloads on Intune | ⭐ Co-mgmt workloads 100 % Intune |
| M14 | Nov 2027 | 4 | Waves done; VPN general retirement | ⭐ ≥ 85 % Entra joined · ⭐ Datacenter refresh avoided |
| M15 | Dec 2027 | 4 | MECM and print servers retired | ⭐ MECM decommissioned ⚠️ · ⭐ Print servers retired |
| M16 | Jan 2028 | 4 → 5 | File servers retired; DCs consolidated | ⭐ File servers retired · ⭐ DCs ≤ 6 |
| M17 | Feb 2028 | 5 | Optimize; Path D residuals | ⭐ Path D conversions complete / exceptions signed |
| M18 | Mar 2028 | 5 | Handover and close | 🚦 Program close · 🚦 Phase 6 business case decision |

---

## 7.2 Month detail

### M1 — October 2026 · *Mobilize & stabilize*

| Workstream | Activities | Owner |
|---|---|---|
| PMO | Charter signed; Steering Committee and Design Authority stood up; RAID log; integrated plan v1; budget lines opened; partner/FastTrack engagement requested | PM |
| Identity | **Day 1:** confirm Entra Connect version ≥ 2.5.79.0 (sync fails otherwise after 30 Sep 2026) and plan an upgrade to current 2.6.x; Connect Health review; export AD inventory; identify Domain Admins & service accounts | ID |
| Endpoint | **Week 1:** confirm ESU Year 1 coverage, purchase **ESU Year 2** for Win10 devices not replaced by Oct 2026 ⚠️; schedule ConfigMgr 2503 → **2609** upgrade (2503 is at/near end of support); enable **tenant attach** and **Endpoint Analytics** data upload | EP |
| Security | Secure Score baseline; request **Defender for Identity** licensing/trial; plan MDI sensors on DCs, AD FS, AD CS, Entra Connect servers | SE |
| Discovery | Enable NTLM auditing (`Restrict NTLM: Audit incoming NTLM traffic` + `Audit NTLM authentication in this domain`) on DCs; enable LDAP diagnostic logging (Event 2889); export GPO backups for Group Policy Analytics | ID + EP |
| Change | Stakeholder map; recruit executive and clinical sponsors (CNO/CMIO) | PM |

**Deliverables:** Program charter, RAID v1, assumption confirmation (00 §4), discovery plan.

### M2 — November 2026 · *Discovery and foundations*

| Workstream | Activities | Owner |
|---|---|---|
| Identity | Create 2 break-glass accounts (FIDO2); remove permanent Global Admins → PIM (if E5 approved, D-01) or minimized assignments; **Authentication methods policy** migration from legacy MFA/SSPR policies; Authenticator number matching enforced; start stale-object cleanup | ID |
| Endpoint | ConfigMgr **2609** upgrade ⭐; deploy **CMG** (VM scale set) with Entra auth; Intune tenant prep: RBAC roles, scope tags, naming standards, enrollment restrictions, Windows automatic enrollment (MDM user scope) for pilot group | EP |
| Endpoint | **Group Policy Analytics** import of all GPOs; produce MDM-support % report; start GPO rationalization | EP |
| Apps | Export MECM app inventory + metering; app owner assignment; rationalization workshops start | EP + AO |
| Data | SharePoint Migration Manager agents installed; full scan of DFS-N; D-05 (archive/delete) decision paper | Data |
| Security | MDI sensors on all DCs, AD FS, AD CS, Entra Connect; Sentinel workspace design | SE |
| Infra | Azure landing zone design (CAF Enterprise-scale); ExpressRoute redundancy order | Cloud |
| Certs | Certificate consumer inventory; Cloud PKI vs. SCEP/PKCS decision (§16) | ID + SE |

🚦 **Phase 0 gate:** governance running; Connect/ESU/ConfigMgr risks closed; discovery collecting ≥ 30 days of data.

### M3 — December 2026 · *Foundations*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | **Enable co-management** (auto-enroll all MECM clients that are Hybrid joined; pilot collection controls workloads); move **no workloads yet** except pilot collection: Compliance policies | EP |
| Endpoint | Hybrid join remaining AD-joined devices (Entra Connect device sync + SCP for target OUs); goal: 100 % Hybrid joined by M5 to enable co-management for all | EP + ID |
| Endpoint | Build **Intune baseline**: Windows security baseline, Defender AV/ASR/firewall (endpoint security), compliance policies, Windows LAPS, BitLocker (silent, escrow to Entra), Edge baseline, M365 Apps baseline | EP |
| Identity | Conditional Access **baseline in report-only**: block legacy auth; require MFA all users; admins phishing-resistant; require compliant/Hybrid device for M365 (report-only); session controls | ID + SE |
| Identity | WHfB **cloud Kerberos trust** design: create `AzureADKerberos` object in each domain (`Set-AzureADKerberosServer`); verify DCs are on supported Windows Server builds | ID |
| Apps | Packaging factory stood up (partner); standards: PSADT v4, Win32, detection rules, naming | EP |
| Security | Freeze on change of GPO security settings (avoid moving targets) | SE |

**Change freeze:** 20 Dec – 4 Jan (holiday + clinical surge).

### M4 — January 2027 · *Foundations: prove the hard parts*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | **Autopilot** profiles (user-driven Entra join; pre-provisioned; self-deploying for kiosks); ESP with blocking apps limited to ≤ 6; **Autopilot device preparation** policy evaluated for standard knowledge-worker build | EP |
| Endpoint | OEM/reseller Autopilot registration agreements (Dell/HP/Lenovo) for all new orders from M5 | EP + Procurement |
| Certs | **Cloud PKI** (E5) or **NDES + Intune Certificate Connector (SCEP)**; trusted root + SCEP profiles; **Cisco ISE** EAP-TLS policy to accept Intune-issued certs ⚠️ | ID + Network |
| Identity | WHfB cloud Kerberos trust enabled for the IT pilot; validate SSO to file shares and Kerberos apps from Entra-joined test devices | ID + EP |
| Identity | AD FS RPT migration analysis using **AD FS application activity report** in Entra | ID |
| Security | MDE onboarding through Intune EDR policy for pilot; ASR rules in **audit** mode org-wide (through MECM for non-pilot) | SE |
| Test | Test tenant: full CA + Intune config replicated for regression testing | EP |

⭐ First Entra-joined, Autopilot-provisioned device connects to corporate Wi-Fi and gets Kerberos SSO to a file share.

### M5 — February 2027 · *IT pilot*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | **IT pilot (150 users)**: 100 Path B (Autopilot reset to Entra joined), 50 Path A new devices. All workloads Intune for pilot collection. | EP |
| Endpoint | Co-mgmt workloads for **all** devices: **Compliance policies** → Intune; **Endpoint Protection** → Intune (pilot collection) | EP |
| Identity | CA policies **on** for IT pilot group; **Staged Rollout** (PHS + Seamless SSO) for IT | ID |
| Data | OneDrive **KFM** (silent move, GPO/Intune) for IT; first two department shares → SharePoint as rehearsal | Data |
| Print | Universal Print connector + 20 printers for IT floor | EP |
| Support | Remote Help enabled (E3+); Service Desk run-books v1; floor-walker training | SD |

🚦 **Foundations gate** (start of M5).

### M6 — March 2027 · *Business pilot; cloud-native by default*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | **Business pilot (600 users)** in Finance, HR, Revenue Cycle (remote coders), Marketing; include macOS (50) and Android Enterprise (100) | EP |
| Endpoint | ⭐ **D-02 effective: all new Windows devices ship Entra joined** through OEM-registered Autopilot | EP |
| Endpoint | Intune Wi-Fi/VPN/certificate profiles to co-managed devices (the Resource access workload is already mandated to Intune from ConfigMgr 2403); **Device configuration** → Intune (pilot collection; this also moves Endpoint Protection) | EP |
| Endpoint | Win10 → Win11 in-place upgrades for remaining capable devices through Intune **feature update policy** | EP |
| Network | ⭐ DirectAccess retired (devices moved to VPN / Private Access pilot) | Network |
| Security | Entra Private Access pilot with IT + business pilot; Quick Access for file servers | SE + Network |
| Apps | Top 150 apps packaged and tested (covers ~85 % of installs) | EP |

🚦 IT pilot exit.

### M7 — April 2027 · *Clinical pilot*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | **Clinical pilot**: 2 units (one ambulatory clinic + one inpatient medical-surgical unit): ~120 assigned devices + ~80 shared clinical workstations | EP + Clinical Informatics |
| Endpoint | Shared-device mode: Entra joined + Imprivata (if vendor-certified for Entra join) **or** Hybrid + co-managed (Path D) | EP |
| Endpoint | Co-mgmt workloads: **Windows Update policies** → Intune (Autopatch) for pilot collections; **Office Click-to-Run apps** → Intune | EP |
| Identity | Staged Rollout expanded to 50 % of users | ID |
| Data | KFM for business pilot users; department shares wave 1 (Finance, HR) | Data |
| Security | CA "require compliant device" **enforced** for pilot groups | SE |

🚦 Business pilot exit.

### M8 — May 2027 · *Production waves begin*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | ⭐ **Wave 1** (Corporate/admin campuses, ~1,500 devices) | EP |
| Endpoint | Android device administrator → Android Enterprise migration (300 devices) | EP |
| Endpoint | Autopatch onboarding for rings whose Windows Update, Device configuration, and Office C2R workloads are on Intune (others get WUfB rings from Intune directly) | EP |
| Identity | Staged Rollout 100 %; RPT migrations continue | ID |
| Data | KFM all knowledge workers begins (silent, staged by group) | Data |
| Security | ASR rules → **block** (with exclusions from audit data) for migrated waves | SE |

🚦 **Clinical pilot exit** ⚠️ (incl. 0 clinical safety events, badge-tap time ≤ baseline).

### M9 — June 2027 · *Scale*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | Waves 2–3 (remote/hybrid workforce, ambulatory clinics group A, ~2,500 devices) | EP |
| Endpoint | Co-mgmt workloads: **Client apps** → Intune for all collections; MECM apps progressively retired | EP |
| Security | ⭐ Legacy EDR uninstalled; MDE in active mode everywhere | SE |
| Data | Department shares waves 2–3 | Data |
| Governance | 🚦 **D-09** MECM retention decision confirmed | ES |

### M10 — July 2027 · *Defederation*

| Workstream | Activities | Owner |
|---|---|---|
| Identity | ⭐ **Convert domain from federated to managed** (`Update-MgDomain` / Entra admin center) ⚠️ after 100 % Staged Rollout validated for ≥ 30 days | ID |
| Endpoint | Waves 4–5 (ambulatory clinics group B, hospital non-clinical, ~2,500 devices) | EP |
| Print | Universal Print waves (admin campuses, clinics) | EP |
| VDI | AVD clinical pilot environment built (if EHR certification received) | EUC |

### M11 — August 2027 · *AD FS retired*

| Workstream | Activities | Owner |
|---|---|---|
| Identity | ⭐ AD FS + WAP decommissioned (after 30-day monitoring of zero AD FS token issuance) | ID |
| Endpoint | Waves 6–7 (hospital non-clinical continued) | EP |
| Endpoint | **Device configuration** workload → Intune for all collections; GPOs unlinked by OU | EP |
| Security | CA require compliant device **enforced org-wide** for M365 (with exclusions for Path D group using Hybrid-joined grant) | SE |

### M12 — September 2027 · *Mid-program review*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | ⭐ ≥ 60 % of Windows fleet Entra joined; **shared clinical (Path D) conversion begins** if vendor certified | EP |
| Endpoint | MECM OSD retired (all rebuilds through Autopilot) | EP |
| Infra | DC consolidation begins: DNS decoupled from DCs at sites; first 6 site DCs demoted | ID |
| Data | File share read-only cutovers continue; 90-day read-only clock starts per share | Data |

🚦 **Mid-program review**: re-baseline, confirm budget, confirm Phase 6 assumptions.

### M13 — October 2027 · *All workloads on Intune*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | ⭐ **All 7 co-management workloads** set to Intune for all devices | EP |
| Endpoint | Waves 8–9 (hospital clinical, unit by unit) | EP |
| Endpoint | MECM client removal begins on Entra-joined devices (Intune remediation script) | EP |

### M14 — November 2027 · *Waves complete*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | ⭐ ≥ 85 % Entra joined; Path C ride-outs scheduled | EP |
| Network | General user VPN retired (Private Access covers app segments) | Network |
| Infra | ⭐ Datacenter refresh avoided (MECM, DP, file, print not refreshed) | Infra |

**Change freeze:** Thanksgiving week.

### M15 — December 2027 · *MECM decommissioned*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | ⭐ **MECM decommission gate** (§5.7) → site uninstall; CMG removed; DPs retired; WSUS removed; DB archived ⚠️ | EP |
| Print | ⭐ Print servers retired (EHR clinical print excepted) | EP |

**Change freeze:** 20 Dec – 4 Jan.

### M16 — January 2028 · *File servers and DC consolidation*

| Workstream | Activities | Owner |
|---|---|---|
| Data | ⭐ File servers retired (read-only period completed; archive in Azure Blob/immutable storage) | Data |
| Identity | ⭐ DC footprint ≤ 6 (4 Azure IaaS + 2 primary datacenter); Seamless SSO computer account removed if no down-level devices | ID |

### M17 — February 2028 · *Optimize*

| Workstream | Activities | Owner |
|---|---|---|
| Endpoint | Path D: remaining shared clinical devices converted or permanent exception signed by CMIO/CISO | EP |
| Endpoint | Endpoint Analytics remediation scripts tuned; Autopatch ring optimization | EP |
| Security | Purple-team exercise against new architecture | SE |

### M18 — March 2028 · *Handover and close*

| Workstream | Activities | Owner |
|---|---|---|
| PMO | Final KPI report; lessons learned; BAU acceptance; benefits realization plan | PM |
| Governance | 🚦 **Program close** + 🚦 **Phase 6 AD retirement business case** | ES |
