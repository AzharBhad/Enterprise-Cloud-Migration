# 19. Pilot Deployment Plan

> **Part C: Execution** · Section 19 of 33 · Lead: Program Manager + Endpoint Team · Related: §6.5 gates, §20 rollout, §21 testing

## 19.1 Pilot philosophy

Each pilot has one job: **find the failure modes that would hurt the next, larger population, while the blast radius is still small.** Pilots aren't demos. A pilot that "succeeds" without surfacing issues was scoped wrong, so give pilots the hard cases deliberately.

| Pilot | When | Size | Population | Primary question it answers |
|---|---|---|---|---|
| **P0 Lab / Ring 0** | M3–M4 | ~30 devices | Endpoint & identity engineers, lab hardware for each model | Does the build work end-to-end technically? |
| **P1 IT pilot (Ring 1)** | M5–M6 | 150 users | IT staff across all IT teams incl. Service Desk, 20 % remote | Can we support it? Are the runbooks right? |
| **P2 Business pilot (Ring 2)** | M6–M7 | 600 users | Finance, HR, Revenue Cycle (remote coders), Marketing, Research (macOS), Supply Chain (Android) | Does it work for real business workflows and diverse apps? |
| **P3 Clinical pilot (Ring 2c)** | M7–M8 | ~120 assigned + ~80 shared devices | 1 ambulatory clinic + 1 inpatient med-surg unit | Is it **safe and fast** for clinical care? |

## 19.2 Pilot scope matrix

| Capability | P0 | P1 | P2 | P3 |
|---|---|---|---|---|
| Autopilot user-driven (new device, Path A) | ✅ | ✅ (50) | ✅ (150) | ✅ (40) |
| Path B Intune wipe → Entra join | ✅ | ✅ (100) | ✅ (450) | ✅ (80) |
| Autopilot pre-provisioning | ✅ | ✅ | ✅ (clinics) | ✅ |
| Autopilot device preparation | ✅ | ✅ (20) | ✅ (50) | — |
| Self-deploying kiosk | ✅ | — | ✅ (10 check-in kiosks, non-PCI) | ✅ (signage) |
| Shared clinical (Entra joined or Path D) | ✅ | — | — | ✅ (80) |
| WHfB cloud Kerberos trust | ✅ | ✅ | ✅ | ✅ |
| CBA / badge tap | ✅ | — | — | ✅ |
| Staged Rollout (PHS) | ✅ | ✅ | ✅ | ✅ |
| CA "require compliant device" enforced | ✅ | ✅ | ✅ | ✅ |
| Cloud PKI Wi-Fi/wired cert | ✅ | ✅ | ✅ | ✅ |
| OneDrive KFM | ✅ | ✅ | ✅ | ✅ |
| SharePoint dept share migration | — | ✅ (IT shares) | ✅ (Finance, HR) | ✅ (clinic admin share) |
| Universal Print | ✅ | ✅ | ✅ | ✅ (non-clinical printers) |
| Entra Private Access | ✅ | ✅ | ✅ | ✅ (EHR segment) |
| Remote Help | ✅ | ✅ | ✅ | ✅ |
| EPM **[E5]** | ✅ | ✅ (developers) | ✅ (research) | ✅ (physicians' dictation) |
| macOS Intune + Platform SSO | ✅ | ✅ (10) | ✅ (50) | — |
| Android Enterprise dedicated (Zebra) | ✅ | — | ✅ (100 supply chain) | ✅ (20 unit devices) |
| iOS shared device mode | ✅ | — | — | ✅ (30) |

## 19.3 Pilot participant selection

| Criterion | Rationale |
|---|---|
| **Coverage of hardware models** | Every model with > 100 devices in the estate appears in P1/P2 |
| **Coverage of top-150 apps** | ≥ 90 % of top-150 apps have at least 3 pilot users |
| **Remote/hybrid mix** | ≥ 20 % remote in P1/P2 (proves off-network Autopilot, Private Access) |
| **Bandwidth extremes** | Include ≥ 2 low-bandwidth clinics in P2 |
| **Willingness + influence** | Champions (§23) recruited from pilot users |
| **Exclusions** | Users on leave, in month-end close (Finance) in the pilot week, executives' assistants in board weeks |
| **Clinical unit selection (P3)** | Nurse manager and medical director sponsorship; stable staffing; no Joint Commission survey or EHR upgrade within ±4 weeks; 24×7 unit (tests shift change) |

## 19.4 Pilot operating model

| Element | P1 / P2 | P3 clinical |
|---|---|---|
| Support | Dedicated Teams channel + priority queue `Pilot-<n>`; floor-walkers on day 1–2 | **At-the-elbow support 24×7 for 72 h**, then day shifts for 2 weeks; clinical informatics nurse on each shift |
| Feedback | Daily survey (3 questions) for 5 days, then weekly; Teams channel; Viva Glint/Forms | Huddle feedback at shift change; Safety event reporting system monitored |
| Daily stand-up | 15 min, engineering + SD + PM | 15 min + clinical lead; **go/no-go for next tranche daily** |
| Issue triage | Severity per §21.6; P1/P2 issues fixed or workaround before next tranche | **Any clinical-safety-affecting issue = automatic pause** |
| Rollback readiness | Runbook tested in P0 | Spare pre-provisioned devices on unit (10 % spare pool) |
| Downtime procedures | — | Unit downtime procedures reviewed with charge nurse; EHR downtime viewer verified |

## 19.5 Pilot exit criteria

| Metric | P1 IT | P2 Business | P3 Clinical |
|---|---|---|---|
| Autopilot first-attempt success | ≥ 90 % | ≥ 95 % | ≥ 97 % |
| Median provisioning time | ≤ 60 min | ≤ 45 min | ≤ 30 min (pre-provisioned user phase) |
| Devices compliant at 24 h | ≥ 95 % | ≥ 97 % | ≥ 98 % |
| Wi-Fi/wired cert auth success | ≥ 99 % | ≥ 99.5 % | ≥ 99.9 % |
| Incidents per migrated device (first 10 days) | ≤ 8 % | ≤ 4 % | ≤ 3 % |
| P1/P2 incidents open | 0 | 0 | 0 |
| App L2/L3 pass for pilot app set | ≥ 95 % | ≥ 98 % | **100 % clinical-critical** |
| User CSAT | ≥ 3.8/5 | ≥ 4.0/5 | ≥ 4.0/5 + nurse manager sign-off |
| Badge tap → EHR ready | — | — | ≤ baseline (target ≤ 30 s) |
| Clinical safety events attributable | — | — | **0** |
| Runbooks updated & SD trained | ✅ | ✅ | ✅ |
| Rollback tested (at least once, real or simulated) | ✅ | ✅ | ✅ |

**Exit decision:** Design Authority recommends → Steering Committee approves (§6.5). The clinical pilot exit also needs **CMIO + CNO sign-off**.

## 19.6 Pilot timeline (P3 clinical example)

| Day | Activity |
|---|---|
| T-30 | Unit walkthrough; device inventory per room/workstation-on-wheels; peripheral inventory (scanners, label printers, signature pads) |
| T-21 | Pre-provisioning of replacement/converted devices at depot; badge tap validation in simulation lab |
| T-14 | Super-user training (2 h hands-on); comms to unit staff |
| T-7 | Dress rehearsal on 2 spare devices on the unit during a quiet shift |
| T-1 | Go/no-go (CMIO, nurse manager, EP lead, SD lead) |
| T-0 | Swap devices in tranches by room/pod (not all at once), 06:00–10:00 avoided (shift change/rounds) |
| T+1 to T+3 | 24×7 at-the-elbow; hourly check-ins day 1 |
| T+14 | Pilot retrospective; exit metrics |

## 19.7 RACI: Pilots

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Pilot scope & participants | I | **A** | C | C | R | C | R | C |
| Technical readiness (P0) | I | I | **A** | R | R | R | I | I |
| Pilot execution | I | **A** | C | R | R | R | C | R |
| Clinical safety oversight (P3) | **A** (with CMIO) | R | C | I | R | I | R | C |
| Feedback & issue triage | I | **A** | C | R | R | R | C | R |
| Exit recommendation | I | R | **A** | C | C | C | C | C |
| Exit approval | **A** | R | C | I | I | I | C | I |
