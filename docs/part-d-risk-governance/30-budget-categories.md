# 30. Budget Categories

> **Part D: Risk, Compliance, Governance** · Section 30 of 33 · Owner: Program Manager + Finance Business Partner
>
> This section defines **categories, cost drivers, and estimation approach**. It doesn't give a priced budget, because pricing depends on EA terms, partner rates, and regional labor costs. Effort estimates (person-weeks) are in Appendix K and feed the labor lines below.

## 30.1 Budget structure

| # | Category | Type | Cost drivers | Estimation basis | Notes |
|---|---|---|---|---|---|
| B1 | **Microsoft licensing uplift** | Opex (recurring) | E3 → E5 step-up (7,300), frontline Defender add-on (1,900), Entra Private Access, Windows 365, Universal Print volume, ESU Year 2 | §29 quantities × EA price | Largest recurring line. Offset by retired tools (B12). |
| B2 | **Azure consumption** | Opex (recurring) | Landing zone, IaaS DCs (4), connectors, UP connector VMs, AVD host pools, Azure Files, archive storage, Sentinel ingestion, Log Analytics, Connected Cache hosts (if Azure), ExpressRoute | Azure Pricing Calculator + Sentinel ingestion estimate (GB/day) | Set budgets and alerts per subscription; reserved instances / savings plans for steady-state VMs |
| B3 | **Hardware** | Capex | Win10-incapable replacements (~900), spare pool (3 % = ~330), FIDO2 keys (admins + no-phone users ≈ 1,500), badge readers if vendor change, Connected Cache hosts | Unit cost × qty | Align with the normal refresh budget; only *accelerated* replacements are program cost |
| B4 | **Partner services: packaging factory** | One-time | ~650 apps; complexity tiers (simple/medium/complex) | Per-app rate × tier mix | Fixed-price per app with acceptance criteria |
| B5 | **Partner services: data migration** | One-time | ~110 TB in scope, ~400 shares, ACL complexity | Per-TB or per-share + PM | Migration Manager is free; labor isn't |
| B6 | **Partner services: architecture/engineering augmentation** | One-time | Identity, Intune, Security SMEs | FTE-months | Plus Microsoft FastTrack (no cost for eligible tenants) and Unified Support hours |
| B7 | **Internal backfill / contractors** | One-time | 6 FTE backfill for 18 months (D-07) | FTE × loaded rate | Protects BAU |
| B8 | **Change management & training** | One-time | Change lead + 2 analysts, content production, LMS, super-user time (clinical backfill hours!), floor-walkers | FTE + content + **clinical backfill hours** | Clinical backfill is often forgotten and is real cost |
| B9 | **Service Desk surge** | One-time | Hypercare staffing (floor-walkers, extended hours) M5–M17 | Wave calendar × staffing ratio | |
| B10 | **Tools** | Opex/one-time | Packaging tools (PSADT is free; Master Packager/Advanced Installer optional), test automation, Power BI Pro/Premium for dashboards, third-party Cloud CA if no E5 | Quotes | |
| B11 | **Vendor certification & remediation** | One-time | Badge SSO vendor professional services, EHR vendor AVD certification, MFP firmware/connector, medical device vendor changes | Vendor SOWs | Often contractually avoidable; negotiate early |
| B12 | **Decommissioning & savings (negative cost)** | Savings | MECM/SQL licenses (if separate), server hardware refresh avoided (~120 servers), SAN capacity, legacy EDR, remote support tool, SMS MFA gateway, Jamf on-prem, AD FS hardware/certs, print server hardware, DirectAccess/VPN capacity | Contract values, avoided capex | Track as benefits realization (§31) |
| B13 | **Security testing & assurance** | One-time | Pen tests (M9, M17), HITRUST readiness, QSA for PCI kiosk change | Quotes | |
| B14 | **Contingency** | Reserve | 10–15 % of one-time costs (B3–B11, B13) | % | Released by Executive Sponsor (§27.6) |

## 30.2 Cost phasing (indicative profile)

| Phase | Dominant categories |
|---|---|
| Phase 0–1 (M1–M5) | B1 (ESU, initial E5/step-ups), B2 (landing zone), B6, B7, B3 (Win10 replacements), B13 |
| Phase 2 (M4–M8) | B4 ramp, B5 start, B8, B9 (pilots) |
| Phase 3 (M7–M14) | B4/B5 peak, B9 peak, B3 (spares), B11 |
| Phase 4–5 (M12–M18) | B12 savings begin; B2 steady-state; B6/B7 ramp-down |

## 30.3 Financial governance

| Control | Detail |
|---|---|
| Budget owner per category | Named in the program finance tracker |
| Monthly variance report | Actual vs. plan per category; > 10 % variance → Steering Committee |
| Azure cost management | Budgets + alerts per subscription; tag policy (`CostCenter`, `Workload`, `Program=CloudMigration`); monthly FinOps review |
| Licensing true-up alignment | Step-ups timed to the EA anniversary where possible |
| Benefits tracking | B12 savings and productivity KPIs (§4.2) tracked in the benefits register; reported quarterly |
| Capitalization | Finance decides on capitalization of one-time implementation labor per accounting policy |

## 30.4 Common budget omissions

1. **Clinical staff backfill hours** for super-user training and at-the-elbow time.
2. **Sentinel ingestion** growth after onboarding DC security events and Defender raw data.
3. **Spare device pool** for Path B swaps and clinical units.
4. **ESU Year 2** for stragglers.
5. **Dual-running costs**: old and new tools overlap 6–12 months (legacy EDR, SIEM, VPN, Jamf, Citrix).
6. **Vendor professional services** for badge SSO, MFPs, and EHR certification.
7. **FIDO2 keys** and replacement stock.
8. **Network upgrades** for clinic local breakout (SD-WAN licensing) as a prerequisite (PR-N2).
