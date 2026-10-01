# 25. Training Plan

> **Part C: Execution** · Section 25 of 33 · Lead: Training Lead (under Change Lead) + Endpoint Team (technical training) · Related: §23, §24, Endpoint Engineer role section

## 25.1 Training needs analysis

| Audience | Size | Need level | Format | Duration | Timing |
|---|---|---|---|---|---|
| End users: knowledge workers | 7,300 | Awareness + how-to | e-learning (micro-videos), quick-reference cards, drop-in clinics | 20 min total | T-2 weeks per wave |
| End users: clinical frontline | 1,900 | How-to (badge tap, shared-device behaviors) | **Huddle demos (5 min)** by super-users, laminated card at workstations, simulation lab sessions | 5–10 min | T-1 week per unit |
| Physicians | 1,100 | How-to + EPM | 3-min video, physician champion demo at medical staff meeting | 5 min | T-2 weeks |
| Remote workforce | 1,300 | How-to (Autopilot at home, Path B self-service) | Video + printed box insert | 15 min | T-2 weeks |
| Champions | 150 | Advanced user + troubleshooting basics | Live virtual workshop + sandbox | 2 h | T-4 weeks |
| Clinical super-users | 120 | Advanced + at-the-elbow support | Hands-on in simulation lab | 4 h | T-3 weeks |
| Managers | 900 | Leading change, talking points | 30-min briefing | 30 min | T-4 weeks |
| **Service Desk Tier 1** | 30 | New tools & processes | Instructor-led + labs + shadowing in P1 | 3 days | Before P1 (M5) |
| **Service Desk Tier 2 / desk-side** | 40 | Deep troubleshooting | Instructor-led + labs | 5 days | Before P1 |
| **Endpoint engineering** | 25 | Intune, Autopilot, Autopatch, Graph/PowerShell automation, macOS/mobile | Microsoft Learn paths + partner-led bootcamp + certifications | 10 days + self-study | M2–M5 |
| Identity engineering | 10 | Entra, CA, PIM, Cloud Sync, Kerberos cloud trust, CBA | Partner-led + Learn | 5 days | M2–M4 |
| Security / SOC | 15 | Defender XDR, Sentinel, KQL, Intune endpoint security | Microsoft Learn + SC-200 | 10 days | M2–M6 |
| Server/infra staff (reskilling) | 30 | Azure fundamentals, Azure Arc, Azure Update Manager, Azure Files | AZ-900 → AZ-104 | 10 days | M6–M12 |

## 25.2 End-user curriculum (micro-modules)

| Module | Length | Content |
|---|---|---|
| U1 "Your new sign-in" | 3 min | WHfB PIN/face setup, passkey in Authenticator, what to do if you forget your PIN (self-service reset) |
| U2 "Where are my files?" | 4 min | OneDrive in File Explorer, Files On-Demand icons, sharing a link instead of an attachment, restore a previous version |
| U3 "Your team's files" | 4 min | SharePoint/Teams libraries, "Add shortcut to My files", co-authoring |
| U4 "Printing" | 2 min | Add a Universal Print printer by location, secure release |
| U5 "Getting apps" | 2 min | Company Portal |
| U6 "Getting help" | 2 min | Remote Help consent screen, how to read the device name, self-service options |
| U7 "New laptop day" | 3 min | Unboxing to desktop (Autopilot), what to expect, how long it takes |
| U8 "Admin rights the new way" (targeted) | 3 min | EPM elevation request and approval flow |
| U9 Clinical: "Shared workstation sign-in" | 3 min | Badge tap, tap-out, fast switching, what the new screens look like |
| U10 Mobile: "Work apps on your phone" | 3 min | App protection PIN, what IT can and can't see on BYOD (privacy) |

Delivery: Viva Learning (or LMS) assignment by wave; available in English and Spanish (plus other languages per workforce demographics); accessibility (captions, screen-reader compatible).

## 25.3 Service Desk training curriculum

| Module | Topics | Lab |
|---|---|---|
| SD1 Entra & sign-in | WHfB, TAP issuance, MFA method reset, sign-in logs (reading error codes), CA "why was I blocked" (sign-in log → CA tab), self-service password reset | Issue TAP; diagnose a CA block |
| SD2 Intune device actions | Device search, sync, restart, retire/wipe (with approvals), **Autopilot reset**, **BitLocker key retrieval**, **LAPS retrieval**, compliance details | Recover a locked-out BitLocker device |
| SD3 Remote Help | Sessions, elevation, RBAC, audit | Live session |
| SD4 Autopilot | ESP errors and codes, reset on error, collecting logs, when to swap device | Fix an ESP failure |
| SD5 OneDrive/SharePoint | Sync errors, KFM status, restore, sharing issues, permission requests | Restore deleted folder |
| SD6 Universal Print | Add printer, job status, connector issues | — |
| SD7 Apps | Company Portal, install failures (IME log basics), EPM requests | Approve an EPM request |
| SD8 Mobile | Company Portal enrollment, MAM wipe, shared device mode | — |
| SD9 Escalation & hypercare process | Wave categories, severity, escalation paths | Tabletop |

**Knowledge base:** ≥ 80 KB articles published before P1, structured by symptom ("I can't sign in after my laptop was refreshed"), each with a resolution owner and review date.

## 25.4 Engineering certification paths (funded)

| Role | Certification | Target date |
|---|---|---|
| Endpoint engineers | **MD-102** Endpoint Administrator; **MS-102** M365 Administrator (leads) | M6 / M12 |
| Identity engineers | **SC-300** Identity and Access Administrator | M6 |
| Security analysts | **SC-200** Security Operations Analyst; **SC-401** Information Security Administrator (data protection) | M6 / M12 |
| Architects | **SC-100** Cybersecurity Architect; **AZ-305** Azure Solutions Architect | M12 |
| Infra reskilling | **AZ-900** → **AZ-104** Azure Administrator | M12 |

> Check certification names and codes against Microsoft Learn at enrollment time. Microsoft retires and renames exams frequently.

## 25.5 Training effectiveness (Kirkpatrick)

| Level | Measure | Target |
|---|---|---|
| L1 Reaction | Post-module rating | ≥ 4.2/5 |
| L2 Learning | Completion; short quiz for SD/champions | Completion ≥ 70 % (users), 100 % (SD); quiz ≥ 80 % |
| L3 Behavior | WHfB usage, Company Portal self-service, OneDrive usage | See §23.6 |
| L4 Results | How-to tickets per 100 migrated users | ≤ 4 in the first 10 days |

## 25.6 RACI: Training

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Training needs analysis | I | **A** | C | C | C | C | C | C |
| End-user content | I | **A** | I | C | R | C | C | C |
| Clinical content | I | **A** | I | I | C | I | R | I |
| Service Desk curriculum | I | C | C | R | R | C | I | **A** |
| Engineering certification plan | **A** (funding) | R | C | R | R | R | I | I |
| Effectiveness measurement | I | **A/R** | I | I | C | I | C | C |

---

**End of Part C.** Next: **Part D: Risk, Compliance, Governance, starting with §26 Risk Register.**
