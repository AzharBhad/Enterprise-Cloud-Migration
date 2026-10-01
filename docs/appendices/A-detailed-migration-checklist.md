# Appendix A: Detailed Migration Checklist

> Master checklist organized by phase and workstream. Each item references the section that defines it. Copy into your tracker (Planner/DevOps/ServiceNow) with owner and due date.
>
> Legend: **[C]** = critical-path item · **[G]** = gate evidence item

## A.1 Phase 0: Mobilize & Stabilize (M1–M2)

### Governance
- [ ] Program charter signed by Executive Sponsor (§31) **[G]**
- [ ] Steering Committee, Design Authority, CAB, Clinical Advisory Group, Security & Compliance Forum constituted, with charters (§31.2)
- [ ] RAID log created; R-0xx and R-1xx/2xx risks loaded with owners (App-G)
- [ ] Assumptions ASM-01…07 confirmed or re-baselined (00 §4) **[G]**
- [ ] Decision papers for D-01, D-02, D-04, D-07, D-11, D-12, D-13 scheduled
- [ ] Microsoft FastTrack / account team / partner engagement initiated
- [ ] HIPAA BAA scope confirmed for all services in the target architecture (PR-A7)

### Day-1 risks
- [ ] **Entra Connect version verified ≥ 2.5.79.0; upgrade to current 2.6.x planned/executed** (R-006) **[C]**
- [ ] **Windows 10 inventory complete; ESU Year 2 purchased for residual devices** (R-001) **[C]**
- [ ] ConfigMgr 2503 → 2609 upgrade scheduled (R-007)
- [ ] Break-glass accounts created and tested (PR-I9)

### Discovery
- [ ] Tenant attach + Endpoint Analytics enabled; baseline captured (EE-1.1 #1)
- [ ] NTLM auditing, LDAP 2889 diagnostics, DNS query logging enabled on DCs (§15.2)
- [ ] Defender for Identity sensors on all DCs, AD FS, AD CS, Entra Connect (PR-C8)
- [ ] GPO backup + Group Policy Analytics import (§14.3)
- [ ] MECM app catalog + metering export; app owners assigned (§11)
- [ ] File share scans (Migration Manager) started (§12)
- [ ] Certificate consumer inventory started (§16.2)
- [ ] Print queue + MFP inventory exported (§12.7)
- [ ] Knowledge capture sprint for MECM, AD, PKI, AD FS (R-029)

## A.2 Phase 1: Foundations (M2–M5)

### Identity (§9)
- [ ] Sync scope cleaned; UPN = mail fixed; stale objects quarantined
- [ ] Authentication methods policy migrated; Authenticator number matching; FIDO2/passkeys/TAP enabled
- [ ] Global Admins reduced; PIM configured **[E5]**; separate cloud-only admin accounts
- [ ] Seamless SSO enabled (bridge); key rollover scheduled
- [ ] **WHfB cloud Kerberos trust**: `AzureADKerberos` object created; policy for Ring 0–1 **[C]**
- [ ] CA baseline (CA001–CA008, CA100–CA113) built in **report-only**; CA insights workbook reviewed (§9.6, §13.2)
- [ ] AD FS RPT classification complete (§9.3.2 step 12)

### Endpoint (§10, §17, §18)
- [ ] ConfigMgr 2609 upgraded **[G]**
- [ ] CMG deployed; co-management enabled (auto-enroll Pilot → All)
- [ ] Hybrid join for remaining AD-joined devices (co-management prerequisite)
- [ ] Intune RBAC roles, scope tags, naming standard, assignment filters, ring groups
- [ ] Multi-admin approval enabled (scripts, apps to All, wipes)
- [ ] Config-as-code repo + nightly export running
- [ ] Windows security baseline, endpoint security policies, compliance policies (all platforms)
- [ ] Tenant setting: devices without compliance policy = **Not compliant**
- [ ] Windows LAPS (Entra backup); BitLocker policy with escrow-before-encryption
- [ ] Autopilot profiles (UD, PP, SD), ESP, device naming, group tags
- [ ] OEM/reseller Autopilot registration contracts in place
- [ ] Autopatch tenant enrollment; groups mapped to rings
- [ ] Remote Help enabled; SD RBAC roles
- [ ] Post-migration health + KFM status scripts deployed (detect-only)

### Certificates & network (§16, §8.3)
- [ ] D-11/D-12 decided; Cloud PKI CAs created (production, HSM-backed) or NDES + connector
- [ ] ISE trusts the new chain; authZ policy for Intune device certs **[C]**
- [ ] SCEP + trusted root + Wi-Fi + wired profiles for Ring 0
- [ ] **First Entra-joined Autopilot device on corporate Wi-Fi** ⭐ **[C][G]**
- [ ] Intune/Autopilot/M365 endpoints allowed; TLS inspection bypassed; proxy SYSTEM-context bypass
- [ ] Onboarding SSID / VLAN for OOBE
- [ ] Delivery Optimization configured; Connected Cache nodes planned
- [ ] Private Network Connectors (≥ 2 per group) deployed

### Security (§13)
- [ ] MDE ↔ Intune connection; security settings management
- [ ] ASR rules in audit org-wide
- [ ] Sentinel workspace + connectors (Entra, Defender XDR, Intune diagnostics)
- [ ] Sensitivity labels + DLP (HIPAA) live before first share migration

### Apps & data (§11, §12)
- [ ] Rationalization (4R) complete for top 300 apps
- [ ] Packaging factory onboarded; standards published; top 150 apps packaged
- [ ] IE mode cloud site list published
- [ ] SharePoint information architecture approved; hub sites created
- [ ] D-05 archive/delete decision approved by Legal

### Operations & people
- [ ] Test tenant mirrors production config (Microsoft365DSC)
- [ ] Runbooks: Path B, Autopilot troubleshooting, workload switch, rollback catalog (§22.2)
- [ ] Service Desk trained (SD1–SD9); ≥ 80 KB articles (§25.3)
- [ ] Champions and super-users recruited (§23.4)
- [ ] Foundations gate passed **[G]**

## A.3 Phase 2: Pilot (M4–M8)
- [ ] P0 lab: E2E-01 … E2E-24 executed (§21.3)
- [ ] P1 IT pilot executed; exit criteria met (§19.5) **[G]**
- [ ] Co-mgmt workloads: Compliance → Intune (all); EP pilot; Device config pilot (§18)
- [ ] Staged Rollout PHS: IT → 25 % → 50 % → 100 %
- [ ] P2 business pilot (incl. macOS, Android Enterprise) exit criteria met **[G]**
- [ ] D-02 effective: all new devices Entra joined
- [ ] DirectAccess retired
- [ ] Clinical sim lab validation of peripherals and badge tap
- [ ] P3 clinical pilot exit + CMIO/CNO sign-off **[C][G]**

## A.4 Phase 3: Scale (M7–M14), repeat per wave (§20.4)
- [ ] T-6w wave scoping (devices, users, apps, dependencies, printers, shares, peripherals)
- [ ] T-5w wave readiness review: app readiness ≥ 98 %, dependency clearance (§15.8)
- [ ] T-4w comms C1, manager toolkit
- [ ] T-3w KFM enabled (throttled ≤ 1,000 devices/day); Autopilot Convert-all; SPO pre-migration
- [ ] T-2w training; SD briefing
- [ ] T-1w comms C4; KFM health remediation; spare devices staged
- [ ] T-2d go/no-go (10 criteria, §20.5) **[G]**
- [ ] T-0 share cutover; Path B tranches; Universal Print; GPO unlink
- [ ] T+1…5 hypercare; daily stand-ups
- [ ] T+10 wave closure report
- [ ] T+90 read-only shares archived; stale objects cleaned

### Program-level Phase 3
- [ ] Defederation (M10) after 30 days at 100 % staged rollout **[C]**
- [ ] AD FS decommissioned after 30 days of zero issuance (M11)
- [ ] All co-mgmt workloads on Intune for all devices (M13) **[G]**
- [ ] Legacy EDR removed (M9); ASR block org-wide
- [ ] CA "require compliant device" enforced org-wide (M11)
- [ ] Android DA → AE complete
- [ ] D-08, D-09, D-10 decided

## A.5 Phase 4: Decommission (M12–M16)
- [ ] MECM decommission gate evidence (§5.7) **[G]**
- [ ] MECM client removed (≥ 99 %); co-mgmt policy removed
- [ ] CMG, DPs, SUP/WSUS, site roles removed; DB archived (12 months)
- [ ] PXE DHCP options removed
- [ ] Print servers retired (EHR print excepted)
- [ ] File servers retired after 90-day read-only + archive snapshot
- [ ] DNS/DHCP decoupled from DCs; per-DC consumer count = 0 before each demotion
- [ ] DCs ≤ 6 (4 Azure IaaS + 2 primary DC)
- [ ] General VPN retired; exceptions group only
- [ ] Second AD CS issuing CA retired; templates rationalized

## A.6 Phase 5: Optimize & Handover (M15–M18)
- [ ] Path D devices converted or exceptions signed (CMIO/CISO)
- [ ] Endpoint Analytics optimization backlog executed (§33)
- [ ] Purple-team exercise; findings remediated
- [ ] KPIs at target (§4.2) **[G]**
- [ ] BAU running 60 days; SLAs met (§32)
- [ ] Runbooks, KB, repo, dashboards handed over and accepted
- [ ] Lessons learned published; benefits register handed to Finance
- [ ] D-14 Phase 6 decision **[G]**
