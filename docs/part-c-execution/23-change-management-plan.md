# 23. Change Management Plan

> **Part C: Execution** · Section 23 of 33 · Lead: Change Lead (OCM) under the Program Manager · Related: §24 Communication, §25 Training

This section covers **organizational change management (OCM)**: people adopting new ways of working. Technical change control (CAB) is covered in §31.

## 23.1 Approach

The plan uses the **Prosci ADKAR** model (Awareness, Desire, Knowledge, Ability, Reinforcement) for each persona. It is tuned for a healthcare workforce where **clinical time is the scarcest resource** and change saturation is real (EHR upgrades, regulatory training, staffing pressure).

**Guiding rules:**
1. **Touch users once per wave.** Bundle device conversion + WHfB + KFM + Universal Print + share cutover into a single user event.
2. **Clinicians learn from clinicians.** Clinical super-users and nurse educators deliver the clinical content, not IT.
3. **Show "what's in it for me" in their language.** For a nurse, that's "badge tap gets you into the EHR faster and you never wait for a PC rebuild", not "Zero Trust".
4. **Measure adoption, not just deployment.** Success means WHfB *used*, files *in* OneDrive, printing *working*, and tickets *down*.

## 23.2 Stakeholder analysis

| Stakeholder group | Size | Impact | Influence | Current sentiment (est.) | Key concern | Engagement strategy |
|---|---|---|---|---|---|---|
| Executive leadership (CEO, CFO, COO) | 12 | Low | **High** | Supportive (cost/risk) | Cost, disruption, cyber risk | Quarterly steering, benefits dashboard |
| CMIO / CNO / clinical leadership | 15 | Medium | **High** | Cautious | Patient safety, clinician time | Co-own clinical pilot; veto on clinical go/no-go |
| Nurse managers / unit directors | 180 | High | High | Skeptical (change fatigue) | Shift disruption, staffing | Unit-level scheduling, at-the-elbow, blackout respect |
| Physicians (employed + affiliated) | 1,100 | Medium | **High** | Mixed | Login speed, dictation tools, admin rights | Physician champion, EPM for their tools, VIP white-glove |
| Clinical frontline (nurses, techs, registration) | 1,900 | **High** | Medium | Neutral | "Will my badge still work?" | Super-users, quick-reference cards, huddles |
| Knowledge workers | 7,300 | Medium | Medium | Neutral/positive | Files, drive letters, printers | Self-service training, clear dates |
| Remote workforce (coders, billing) | 1,300 | Medium | Low | Positive (VPN pain) | Downtime on productivity metrics | Productivity-target relief on conversion day |
| Research | 400 | Medium | Medium | **Resistant** (admin rights, Linux/macOS freedom) | Loss of control | EPM, research exceptions process, early co-design |
| IT staff (EUC, server, network) | 220 | **High** | Medium | Mixed (job security) | Role changes, MECM skills obsolete | Reskilling plan, certification funding (see the Endpoint Engineer role section) |
| Service Desk | 45 | **High** | Medium | Anxious (volume) | Ticket surge, new tools | Early training, staffing uplift during waves |
| Compliance / Privacy / Legal | 10 | Low | High | Supportive (evidence) | PHI exposure, retention | Early review of labels, archive decisions |
| Unions / labor relations (if applicable) | — | — | Medium | — | Monitoring perception (Endpoint Analytics, Remote Help) | Transparent privacy notice |
| Vendors (EHR, badge SSO, MFPs, medical devices) | ~25 | — | High (blockers) | — | Certification, support | Formal engagement, contract leverage |

## 23.3 Change impact assessment

| Change | Personas impacted | Degree | What changes for the user |
|---|---|---|---|
| Entra join + Autopilot | All Windows users | Medium | New sign-in screen; device naming; possibly a new device |
| WHfB / passkeys | All | Medium | PIN/face instead of password; registration once |
| Badge tap on Entra-joined shared devices | Clinical | **High** | Possibly a different tap flow / prompts |
| OneDrive KFM, no H: | Knowledge workers | Medium | Files in OneDrive; "cloud" icons; Files On-Demand |
| Shares → SharePoint/Teams, no drive letters | Departments | **High** | Different navigation; links instead of paths; co-authoring |
| Universal Print | All | Low–Medium | Printer names change; add printers by location |
| Company Portal instead of Software Center | All | Low | Different self-service store |
| No local admin (EPM) | Physicians, research, IT | **High** (perceived) | Elevation requests instead of admin rights |
| Private Access instead of VPN | Remote users | Low (positive) | No VPN connect step |
| MFA method change (no SMS) | ~30 % users | Medium | Authenticator / passkey registration |
| BYOD MAM tightening | BYOD users | Medium | App protection PIN; restrictions on copy/paste |

## 23.4 Change network

| Role | Number | Commitment | Responsibilities |
|---|---|---|---|
| Executive sponsor (CIO) + clinical co-sponsor (CMIO) | 2 | 2 h/month | Visible sponsorship, video messages, remove blockers |
| Change lead | 1 FTE | Full-time | OCM plan, network management, adoption metrics |
| Change analysts | 2 FTE | Full-time (M4–M15) | Wave readiness, impact assessments, comms production |
| **Champions** (business) | ~150 (1 per 60 users) | 2 h/week during their wave | Early access, peer help, feedback |
| **Clinical super-users** | ~120 (≥ 2 per unit per shift pattern) | 4 h training + at-the-elbow on go-live | Clinical floor support, huddle messages |
| Physician champions | 10 | 1 h/month | Physician messaging, EPM rules validation |
| Nurse educators | 8 | Embedded in training | Clinical training content |

## 23.5 Resistance management

| Expected resistance | Root cause | Response |
|---|---|---|
| "I need admin rights" | Control, past bad experiences with IT tickets | EPM with fast support-approved elevation (SLA 15 min during business hours), pre-approved rules for top tools, data showing actual elevation needs |
| "I can't find my files" (drive letters gone) | Habit, mental model | OneDrive shortcuts to SharePoint libraries in File Explorer, "where did my H: go" card, Teams pinned channels, champions |
| "Badge tap is slower" | Real or perceived performance regression | Measure (stopwatch study), publish numbers, fix before scaling (gate) |
| "IT is taking my Mac/Linux freedom" (Research) | Autonomy | Co-design research exception policy; developer persona with Dev Drive, WSL, EPM |
| "Another IT change during staffing shortages" | Change saturation | Blackout calendar, bundling, minimal unit time, at-the-elbow |
| IT staff resistance (MECM experts) | Skills obsolescence, job security | Reskilling, certification funding, new roles (§EE career growth), explicit "no layoffs from automation" message **[DECISION D-13]** |

## 23.6 Adoption metrics (Reinforcement)

| Metric | Source | Target |
|---|---|---|
| WHfB / passkey sign-in share | Entra sign-in logs (authentication method) | ≥ 80 % of interactive Windows sign-ins |
| Files in OneDrive vs. local-only | OneDrive sync reports | KFM 100 %; < 5 % of devices with large local-only data |
| SharePoint/Teams usage of migrated departments | M365 usage reports | Active usage ≥ 80 % of department within 30 days |
| H: / legacy share access attempts after cutover | File server audit (read-only period) | Trending to 0 within 30 days |
| Company Portal installs vs. tickets for software | Intune + ITSM | ≥ 70 % self-service |
| "How-to" tickets per wave | ITSM | Back to baseline within 3 weeks |
| User sentiment | Pulse survey | ≥ 4.0/5 at T+30 |

## 23.7 RACI: Change management

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| OCM strategy | **A** | R | C | I | C | I | C | C |
| Stakeholder engagement | R | **A** | C | I | C | I | R | I |
| Change impact assessments | I | **A** | C | C | R | C | R | C |
| Champion/super-user network | C | **A** | I | I | C | I | R | R |
| Resistance management | R | **A** | C | I | C | C | R | C |
| Adoption measurement | I | **A** | C | C | R | I | C | R |
