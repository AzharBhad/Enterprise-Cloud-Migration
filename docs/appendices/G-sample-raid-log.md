# Appendix G: Sample RAID Log (Risks, Assumptions, Issues, Dependencies)

> A sample snapshot **as of M3 (December 2026)**. The live RAID log sits in a SharePoint list or Dataverse table with the schema below. Program-level risks are referenced from §26 (R-0xx) and the Risk Analysis section (R-1xx/2xx).

## G.1 Schema

| Field | Description |
|---|---|
| ID | `R-`, `A-`, `I-`, `D-` + number (decisions use `D-xx` in the Decision Log; dependencies here use `DEP-`) |
| Type | Risk / Assumption / Issue / Dependency |
| Title, Description | — |
| Workstream | ID / EP / AP / DT / SE / NW / CH / SD / PMO |
| Owner | Named individual |
| Raised / Due / Closed | Dates |
| Probability, Impact, Severity | Risks only (§26.1) |
| Priority | Issues: P1–P4 |
| Status | Open / Monitoring / Mitigating / Closed / Realized (risk → issue) |
| Trigger / KRI | Risks |
| Mitigation / Action | — |
| Contingency | Risks |
| Linked items | Other RAID IDs, decisions, change requests |
| Last reviewed | Date |

## G.2 Risks (sample)

| ID | Title | WS | Owner | P | I | Severity | Status | Trigger / KRI | Mitigation | Contingency | Due |
|---|---|---|---|---|---|---|---|---|---|---|---|
| R-001 | Win10 ESU Year 1 expiry | EP | Endpoint Lead | H | 5 | Critical | **Mitigating** | Win10 devices without ESU/upgrade > 0 | ESU Y2 bought (1,640 devices); 420 upgraded to Win11; 900 replacements ordered | Isolate on restricted VLAN | M6 |
| R-002 | Hidden auth dependencies | ID | Identity Lead | H | 5 | Critical | Mitigating | Open priority ≥ 60 deps per wave | MDI + 2889 + NTLM audit live 75 days; 312 dependencies registered | Path D | M8 |
| R-003 | Badge SSO vendor Entra-join certification | EP/AO | Clinical Apps Mgr | M | 4 | High | **Open ▲** | Not certified by M4 | Vendor upgrade SOW signed; lab test scheduled Jan | Path D for shared clinical | M4 |
| R-018 | Certs for Entra-joined Wi-Fi | ID/NW | Network Lead | M | 5 | Critical | Mitigating | Wi-Fi auth success < 99 % in Ring 0 | Cloud PKI BYOCA created; ISE 3.x policy in lab | Onboarding SSID | M4 |
| R-030 | Program capacity | PMO | PM | H | 4 | Critical | Monitoring | FTE capacity < 85 % | 4 of 6 backfills hired; partner surge clause | Re-baseline waves | M3 |
| R-104 | Hostname consumers break with Autopilot naming | EP | Endpoint Eng. | H | 3 | High | Open | Consumers found in ISE, firewall, licensing | Inventory 60 % done | Naming template change | M4 |
| R-127 | MFP scan-to-SMB & LDAP address book | EP | Print Lead | H | 3 | High | Open | 2889 events from MFP subnet | Firmware program; UP scan pilot | Azure Files landing share | M6 |
| R-182 | Slow first sign-in on shared clinical | EP | Clinical Informatics | H | 4 | High | Open | Badge tap → EHR > 30 s in pilot | Shared PC settings; disable WHfB on shared | Path D | M7 |

## G.3 Assumptions (sample)

| ID | Assumption | Owner | Validation method | Due | Status | Impact if false |
|---|---|---|---|---|---|---|
| A-01 | (ASM-03) Device counts by platform ± 5 % | Endpoint Lead | Reconciled inventory (Intune ∪ MECM ∪ Jamf ∪ MDE) | M1 | ✅ Validated (11,140 Windows) | Wave sizing |
| A-02 | (ASM-04a) Entra Connect ≥ 2.5.79.0 | Identity Lead | Connect version check | Day 1 | ✅ Was 2.5.79.0, upgraded to 2.6.92.0 | Sync outage |
| A-03 | (LIC-1) Tenant provisioned with July 2026 E3/E5 Intune capabilities | Licensing | Message Center + admin center check | M2 | ✅ Remote Help/Advanced Analytics visible | Remote Help budget |
| A-04 | (LIC-2) F3 users on shared PCs don't need desktop Office | Clinical Apps Mgr | Clinical workflow review | M3 | ⏳ In review | +1,900 E3 step-ups |
| A-05 | EHR vendor will certify AVD by M9 | EUC Lead | Vendor letter | M6 | ⏳ Open | Citrix retained (D-10) |
| A-06 | ISE version supports Intune compliance API integration | Network Lead | Vendor TAC confirmation | M2 | ❌ **Invalid**: upgrade needed → Issue I-004 | Cert-only authZ |
| A-07 | Universal Print pooled allowance covers ~1.1 M jobs/month | Print Lead | Print server logs | M3 | ⏳ | Add-on volume |

## G.4 Issues (sample)

| ID | Issue | WS | Owner | Priority | Raised | Status | Action | Due |
|---|---|---|---|---|---|---|---|---|
| I-001 | 214 devices have BitLocker keys only in MECM (not escrowed) | EP | Endpoint Eng. | P2 | M3 | In progress | Escrow remediation script deployed; 168/214 done | M3 +2w |
| I-002 | ConfigMgr 2609 upgrade prerequisite failed on SQL compatibility level | EP | MECM Admin | P2 | M2 | Closed | SQL CU applied; upgrade complete | — |
| I-003 | 37 GPOs set `WUServer` on OUs mixing pilot and non-pilot devices | EP | Endpoint Lead | P3 | M3 | Open | Split OU / security filter before WU workload switch | M6 |
| I-004 | ISE version lacks the required MDM compliance integration | NW | Network Lead | P2 | M2 | Open | ISE upgrade change scheduled M3; cert-attribute authZ meanwhile | M3 |
| I-005 | Packaging factory backlog: 40 apps failed L2 on standard user | EP | Packaging Lead | P3 | M3 | Open | Triage with app owners; App Assure engaged for 6 | M4 |
| I-006 | Clinical blackout calendar conflicts with W8 (respiratory surge) | PMO | PM | P3 | M3 | Open | Move W8 by 3 weeks; Steering informed | M4 |

## G.5 Dependencies (sample)

| ID | Dependency | Type | From → To | Owner | Needed by | Status | Impact if late |
|---|---|---|---|---|---|---|---|
| DEP-001 | Clinic SD-WAN local breakout | Internal (network program) | Network program → Waves W3–W5 | Network PM | M7 | 🟡 Amber: 28/42 clinics | Bandwidth risk R-023 |
| DEP-002 | Azure landing zone (identity subscription) | Internal | Cloud team → IaaS DCs, connectors | Cloud Lead | M3 | 🟢 | Connector placement |
| DEP-003 | EHR upgrade (version N+1) freeze window | Internal (clinical IT) | EHR program → clinical waves | EHR PM | M11 | 🟢 Calendar agreed | Clinical waves slip |
| DEP-004 | Badge SSO vendor release with Entra-join support | External | Vendor → Path D conversion | Clinical Apps Mgr | M10 | 🔴 Not yet released | R-003 |
| DEP-005 | OEM Autopilot registration (reseller) | External | Reseller → Path A | Procurement | M5 | 🟢 Live | Manual hash capture |
| DEP-006 | HITRUST assessor schedule | External | Assessor → compliance evidence | Compliance | M12 | 🟢 | R-037 |
| DEP-007 | Microsoft FastTrack Intune engagement | External | Microsoft → Foundations | PM | M2 | 🟢 | Design support |
| DEP-008 | HR system API for inbound provisioning | Internal | HRIS team → Identity | HRIS Lead | M9 | 🟡 | Lifecycle automation delay |

## G.6 RAID governance

| Activity | Cadence | Forum |
|---|---|---|
| Add/update items | Continuous | Workstream leads |
| Review all open items | Weekly | PMO RAID review |
| Escalate Critical/High, triggers hit, P1/P2 issues, red dependencies | Within 24 h / monthly | Executive Sponsor / Steering |
| Archive closed items | Monthly | PM |
