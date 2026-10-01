# 20. Production Rollout Plan (Waves and Rings)

> **Part C: Execution** · Section 20 of 33 · Lead: Program Manager + Endpoint Team · Related: §5.8 wave principles, §21 testing, §22 rollback

## 20.1 Rings vs. waves

These two concepts are often confused, so the blueprint keeps them separate on purpose:

| Concept | What it governs | Membership | Cadence |
|---|---|---|---|
| **Ring** (deployment ring) | *Change propagation*: policy changes, app updates, Autopatch, CA changes | Stable; device extension attribute `Ring0`–`Ring5` | Continuous, forever (BAU) |
| **Wave** (migration wave) | *One-time migration*: Path A/B conversion, KFM, share cutover, Universal Print, GPO unlink | Department-within-site groups | Once per device, M8–M14 |

A device is assigned a ring at enrollment, which never changes unless its role does. It is assigned a wave once and leaves the wave tracker after hypercare.

## 20.2 Ring definitions (BAU change)

| Ring | % of fleet | Composition | Change soak before next ring |
|---|---|---|---|
| Ring 0 Test | ~0.2 % | Lab + engineers' second devices | 1–2 days |
| Ring 1 IT | ~1.5 % | IT (P1 population) | 2–3 days |
| Ring 2 Early adopters | ~8 % | P2 + P3 participants + champions from every department | 3–5 days |
| Ring 3 Early majority | ~25 % | Corporate, remote workforce | 5 days |
| Ring 4 Broad | ~55 % | Remaining knowledge workers, clinics | 5–7 days |
| Ring 5 Clinical critical | ~10 % | Shared clinical, ED/ICU/OR, kiosks, PCI | after Ring 4 + clinical change window |

## 20.3 Wave plan

| Wave | Month | Population | Devices (approx.) | Paths | Special considerations |
|---|---|---|---|---|---|
| W1 | M8 | Corporate HQ: Finance, HR, IT residual, Legal, Exec office | 1,300 | A, B | Finance close blackout (business days 1–5) |
| W2 | M9 | Remote/hybrid workforce (Revenue Cycle, coders, call center) | 1,100 | A, B | Ship-to-home Path A; Path B remote wipe with KFM gate |
| W3 | M9 | Ambulatory clinics group A (14 clinics) | 1,000 | A, B, D(shared front desk) | Low bandwidth: pre-provisioning at depot + Connected Cache |
| W4 | M10 | Ambulatory clinics group B (14 clinics) + Research | 1,100 | A, B; macOS | Research Linux/macOS stream |
| W5 | M10 | Ambulatory clinics group C (14 clinics) + Admin campuses | 1,000 | A, B | |
| W6 | M11 | Hospital 1 & 2 non-clinical (admin, pharmacy back-office, lab office, facilities) | 1,200 | A, B | Lab instrument PCs → Path D |
| W7 | M11 | Hospital 3 & 4 non-clinical | 1,100 | A, B | |
| W8 | M12–M13 | Hospital clinical assigned devices (physician offices, nursing leadership), unit by unit | 1,100 | A, B | Clinical blackout calendar |
| W9 | M13 | Kiosks, signage, check-in (incl. PCI) | 450 | A (self-deploying, re-provisioned) | PCI change control, QSA notified |
| W10 | M12–M17 | **Shared clinical workstations** (Path D → Entra joined when certified) unit by unit | 1,800 | B/D | Vendor certification gate (R-003) |
| Mobile | M8–M12 | Android DA → AE, iOS shared device mode, BYOD MAM tightening | ~3,000 | — | Separate mobile stream |
| VDI | M10–M16 (gated) | AVD clinical pools, W365 contractors | 650 | — | D-10 |

> **Totals:** W1–W8 = 8,900 assigned Windows devices (macOS counted separately in W4). W9 = 450 and W10 = 1,800, so 11,150 Windows endpoints overall (VDI separate).
>
> **Wave size cap:** ≤ 1,000 devices per week for Path B; Path A follows hardware delivery. Path C devices are co-managed with all workloads on Intune and wait for refresh.

## 20.4 Wave lifecycle (T-minus plan)

| When | Activity | Owner |
|---|---|---|
| **T-6 weeks** | Wave scoping: device list, user list, app set, dependencies (§15), printers, shares, peripherals | PM + EP |
| T-5 weeks | **Wave readiness review**: app readiness ≥ 98 % (§11.8), dependency clearance, share migration plan, Universal Print queues ready | AR |
| T-4 weeks | Comms #1 (what's coming, why, what you need to do); manager briefing | Change |
| T-3 weeks | **KFM enabled** for wave users (silent, throttled); SharePoint pre-migration passes start; Autopilot conversion (Convert-all) for wave devices | EP + Data |
| T-2 weeks | Training available (§25); champions briefed; Service Desk briefing | Change + SD |
| T-1 week | Comms #2 (your date and time); KFM health check → remediation for failures; final delta passes | EP |
| **T-2 days** | **Go/no-go meeting** (§20.5) | PM |
| T-1 day | Comms #3 (reminder: leave device on, plugged in, on network; save work) | Change |
| **T-0** | Share cutover (read-only + final delta); Path B wipes in scheduled tranches; Path A device handout or ship; Universal Print + drive mapping switch; GPO unlink for wave OU/group | EP + Data |
| T+1 to T+5 | **Hypercare** (§20.6); daily wave stand-up; issue burn-down | SD + EP |
| T+10 | Wave closure report; KPIs; lessons learned into next wave | PM |
| T+90 | Read-only shares archived/decommissioned; stale objects cleaned | Data + ID |

## 20.5 Go/no-go criteria (per wave)

| # | Criterion | Threshold |
|---|---|---|
| 1 | Previous wave hypercare exit met | Incident rate ≤ 3 %, 0 open P1/P2 |
| 2 | Wave app readiness | ≥ 98 % weighted; 100 % critical |
| 3 | Dependency clearance | 0 open priority ≥ 60 dependencies for the wave |
| 4 | KFM protected | ≥ 98 % of wave devices (the rest excluded from Path B until fixed) |
| 5 | Autopilot profile assigned | 100 % of Path B devices |
| 6 | Service Desk capacity | Staffing plan for 3 % incident rate + 20 % buffer |
| 7 | No blackout conflict | Calendar check (finance close, clinical events, EHR changes, holidays) |
| 8 | Comms & training delivered | Comms #1–#2 sent, training completion ≥ 60 % |
| 9 | Rollback ready | Spare devices, GPO backups, share ACL restore script tested |
| 10 | Security posture | No active Sev-1 security incident in progress |

**Decision rights:** PM chairs; AR, EP lead, SD lead, Change lead, and the business owner of the wave vote. Any one of the clinical lead, CISO, or SD lead can veto.

## 20.6 Hypercare model

| Element | Design |
|---|---|
| Duration | 5 business days (10 for clinical waves) |
| Staffing | Floor-walkers 1 per 75 users on day 1, 1 per 150 on days 2–3; remote hypercare bridge (Teams) |
| Queue | ITSM category `CloudMigration-Wave<n>`; P2+ escalates directly to the engineering on-call |
| Daily | 08:30 wave stand-up; 16:30 issue review; top-5 issues published to champions |
| Tools | Remote Help, Intune device actions, Autopilot report, Endpoint Analytics, KFM report |
| Exit | Incident rate back to ≤ 1.2× pre-wave baseline; 0 P1/P2; CSAT ≥ 4.0 |

## 20.7 Rollout dashboard (wave tracker)

| Column | Source |
|---|---|
| Device / user / wave / path / ring | Wave tracker (Dataverse / SharePoint list) |
| Hybrid / Entra join state | Entra (Graph `trustType`) |
| Co-mgmt workload authority | MECM / Intune `managementAgent` |
| KFM status | Remediation script output → Log Analytics |
| Compliance | Intune |
| Autopilot status | Autopilot deployment report |
| Wi-Fi cert issued | Intune cert report / ISE logs |
| Incidents (10 days) | ITSM |

## 20.8 RACI: Production rollout

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Wave planning & scheduling | I | **A/R** | C | C | R | C | C | C |
| Wave readiness review | I | R | **A** | C | R | C | R | C |
| Go/no-go | I | **A** | R | C | R | R (veto) | R | R (veto) |
| Wave execution (devices) | I | C | I | C | **A/R** | I | I | R |
| Wave execution (data) | I | C | I | I | **A/R** | I | C | C |
| Hypercare | I | C | I | C | R | C | I | **A/R** |
| Wave closure & lessons learned | I | **A/R** | C | C | R | C | C | R |
