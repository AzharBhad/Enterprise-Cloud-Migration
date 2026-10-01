# Appendix E: Executive Steering Committee Dashboard (Layout + Metrics)

> Audience: Executive Sponsor, Steering Committee, Board summary. Format: **Power BI** (one page, plus drill-through), refreshed daily, reviewed monthly. Design rule: **a leader should understand program health in 60 seconds.**

## E.1 Layout

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│  CONTOSO HEALTH — CLOUD TRANSFORMATION        Month: M9 (Jun 2027)   Overall: 🟡 AMBER │
├───────────────┬───────────────┬───────────────┬───────────────┬──────────────────────┤
│ SCHEDULE      │ BUDGET        │ SCOPE         │ RISK          │ CLINICAL SAFETY      │
│ 🟢 On track    │ 🟡 +6% (B4)    │ 🟢 Stable      │ 🟡 2 Critical  │ 🟢 0 events (YTD)    │
├───────────────┴───────────────┴───────────────┴───────────────┴──────────────────────┤
│  MILESTONES (roadmap strip, M1 … M18) ● done  ◐ in progress  ○ planned  ✖ late      │
│  ●Kickoff ●ESU ●CM2609 ●Co-mgmt ●Wi-Fi cert ●IT pilot ●Biz pilot ●Clinical ◐Waves …  │
├───────────────────────────────────────────┬──────────────────────────────────────────┤
│  OUTCOME KPIs (actual vs. target, trend)  │  MIGRATION PROGRESS                      │
│  • Phishing-resistant users   48% / 50%   │  Windows devices Entra joined  ████░ 41%  │
│  • Secure Score               63% / 65%   │  Co-mgmt workloads on Intune   █████ 5/7  │
│  • Device compliance          96% / 97%   │  Users on OneDrive KFM         ████░ 72%  │
│  • Patch currency (14 d)      93% / 95%   │  Shares migrated (TB)          ███░░ 38/110│
│  • Provisioning time (median) 52m / 45m   │  On-prem EUC servers retired   █░░░░ 21/150│
│  • Tickets / 100 users        29 / 26     │  Waves complete                ███░░ 3/10 │
├───────────────────────────────────────────┼──────────────────────────────────────────┤
│  TOP 5 RISKS (owner, trend, action)       │  DECISIONS NEEDED THIS MONTH              │
│  R-003 Badge SSO cert. ▲ CMIO   due M10   │  D-09 Confirm MECM decommission (M15)     │
│  R-002 Hidden deps     ▼ ID     on plan   │  D-10 VDI platform (AVD vs. Citrix DaaS)  │
│  R-035 SD capacity     ▲ SD     +2 FTE    │                                           │
├───────────────────────────────────────────┴──────────────────────────────────────────┤
│  BENEFITS: avoided capex $ (to date) · retired contracts · hours saved (provisioning)  │
└──────────────────────────────────────────────────────────────────────────────────────┘
```

## E.2 Metric definitions

| Tile | Metric | Definition | Source | Target | RAG rule |
|---|---|---|---|---|---|
| Schedule | Milestone variance | Critical-path milestones late (days) | Integrated plan | 0 | 🟢 ≤ 10 d · 🟡 11–30 d · 🔴 > 30 d |
| Budget | Spend variance | (Actual + forecast) vs. budget, by category | Finance tracker | ±0 % | 🟢 ≤ 5 % · 🟡 5–10 % · 🔴 > 10 % |
| Scope | Approved change requests impacting scope | Count & impact | Change log | — | 🔴 if unfunded |
| Risk | Critical/High open risks | Count; triggers hit | RAID | — | 🔴 any Critical trigger hit |
| Clinical safety | Safety events attributable | Count | Safety reporting | 0 | 🔴 ≥ 1 |
| Phishing-resistant users | Users with ≥ 1 phishing-resistant method used in 30 days ÷ active users | Entra authentication methods activity | §4.2 | 🟢 ≥ target · 🟡 within 10 pts · 🔴 > 10 pts below |
| Secure Score | Microsoft Secure Score % | Defender | §13.7 | same rule |
| Device compliance | Compliant ÷ managed | Intune | 97 % | 🟢 ≥ 97 · 🟡 94–97 · 🔴 < 94 |
| Patch currency | Windows on latest security update within 14 days | WUfB reports | 95 % | 🟢 ≥ 95 · 🟡 90–95 · 🔴 < 90 |
| Provisioning time | Median power-on → desktop | Autopilot report | 45 min | 🟢 ≤ 45 · 🟡 45–60 · 🔴 > 60 |
| Tickets per 100 users | Monthly incidents ÷ users × 100 | ITSM | 22 (M18) | Trend-based |
| Entra joined % | Windows Entra joined ÷ Windows fleet | Entra | Per §7 milestones | vs. plan curve |
| Co-mgmt workloads | Workloads at Intune (all collections) | MECM | 7/7 by M13 | vs. plan |
| KFM % | Users with KFM protected ÷ in scope | Remediation output | 100 % M12 | vs. plan |
| Shares migrated | TB migrated ÷ TB in scope | Migration Manager | 110 TB by M14 | vs. plan |
| Servers retired | EUC servers decommissioned ÷ target | CMDB | ≥ 150 by M16 | vs. plan |
| Benefits | Cumulative savings & avoided costs | Benefits register | Plan | vs. plan |

## E.3 Data pipeline

| Source | Method |
|---|---|
| Intune, Entra, Autopilot | Graph API → Azure Function/Logic App → Log Analytics or Dataverse (daily) |
| WUfB reports, Intune diagnostics, Sentinel | Log Analytics workspace (native) |
| Secure Score | Graph `security/secureScores` (daily) |
| ITSM | Native connector / export |
| Plan, RAID, budget, benefits | SharePoint lists / Project / Dataverse |
| Power BI | Scheduled refresh; row-level security not required (no PHI); **no device names or user names on the executive page** |
