# Appendix K: Estimated Effort by Workstream

> **Indicative** estimates for the 18-month program, based on the baseline assumptions (00) and typical productivity rates. Use them for budgeting (§30) and resourcing (D-07), and **re-estimate after discovery (M3)**. Accuracy at M1 is **±25–30 %**.
>
> Units: **person-weeks (pw)**, 1 pw = 5 working days. T-shirt sizes: **S** < 50 pw · **M** 50–150 · **L** 150–300 · **XL** > 300.

## K.1 Summary

| Workstream | Internal pw | Partner/contract pw | Business/clinical pw | **Total pw** | Size | Peak FTE |
|---|---|---|---|---|---|---|
| PMO & governance | 150 | 20 | — | **170** | L | 2.5 |
| Architecture / Design Authority | 80 | 30 | — | **110** | M | 1.5 |
| Identity modernization (§9, §15 identity side) | 160 | 40 | — | **200** | L | 4 |
| AD dependency assessment & remediation (§15) | 60 | 30 | 40 (app owners) | **130** | M | 3 |
| Endpoint transformation incl. Autopilot, co-mgmt, Autopatch, mobile/mac (§10, §17, §18) | 380 | 60 | — | **440** | XL | 8 |
| GPO → Intune (§14) | 50 | 10 | — | **60** | M | 2 |
| Application packaging (§11) | 40 | 350 | — | **390** | XL | 10 (partner) |
| Application testing & UAT (§11.7) | 30 | 20 | 130 | **180** | L | 6 |
| Data migration: KFM, SharePoint, Azure Files, archive (§12) | 70 | 160 | 30 (data owners) | **260** | L | 6 |
| Print modernization (§12.7) | 25 | 15 | — | **40** | S | 2 |
| Certificates & network integration (§16, §8.3) | 60 | 20 | — | **80** | M | 3 |
| Security transformation (§13) | 170 | 40 | — | **210** | L | 4 |
| Testing & QA (§21) | 80 | 20 | — | **100** | M | 3 |
| Wave execution: deployment techs, desk-side (§20) | 100 | 60 | — | **160** | L | 10 (wave peaks) |
| Service Desk surge & hypercare (§20.6) | 120 | 40 | — | **160** | L | 8 (wave peaks) |
| Change, comms, training (§23–25) | 120 | 60 | — | **180** | L | 4 |
| Clinical super-users & at-the-elbow | — | — | 85 | **85** | M | 20 (go-live days) |
| VDI (AVD/W365) (§10.3.4), gated | 40 | 30 | 10 | **80** | M | 3 |
| Decommissioning: MECM, AD FS, file, print, DCs | 50 | 10 | — | **60** | M | 3 |
| **Total** | **≈ 1,785** | **≈ 1,015** | **≈ 295** | **≈ 3,095** | | |

> ≈ 3,100 person-weeks over ~78 weeks ≈ **40 FTE average**, peaking at ~55–60 during M8–M13 (waves + packaging + data migration overlap).

## K.2 Estimation basis (key drivers)

| Workstream | Driver | Assumption |
|---|---|---|
| Packaging | 650 apps | 50 % simple (1.5 d), 35 % medium (3 d), 15 % complex (6 d) ≈ 1,755 days ≈ 350 pw + 10 % rework |
| App testing | 650 apps | ~1 day app-owner testing per app (L2), plus L3/L4 for ~120 clinical-critical apps |
| Data migration | ~400 shares, 110 TB in scope, 8,900 KFM users | ~2 days per share (scan, owner workshop, permission mapping, cutover, validation) + KFM operations + IA |
| Wave execution | ~5,500 Path B + ~3,000 Path A + 450 kiosks + 1,800 shared | Tech time 0.5 h (Path B remote-assisted), 0.75 h (Path A handout), 1.5 h (shared clinical incl. peripherals) |
| Hypercare | 10 waves + 3 pilots | ~6 people × 2 weeks per wave + pilots |
| Endpoint engineering | Platform build + 10 waves + 7 workloads + mobile/mac | 6–8 engineers sustained for ~60 weeks |
| Clinical | 120 super-users | 4 h training + ~3 days at-the-elbow each |
| Identity | Defederation, 85 RPTs, WHfB CKT, CA, PIM, service accounts | ~0.5–1 pw per RPT; service account remediation 1,350 accounts at ~0.1 d each + complex cases |

## K.3 Effort profile by phase

| Phase | Months | Share of total effort | Dominant activities |
|---|---|---|---|
| 0 Mobilize | M1–M2 | 5 % | Discovery, governance, day-1 fixes |
| 1 Foundations | M2–M5 | 18 % | Platform build, identity, certs, packaging ramp |
| 2 Pilot | M4–M8 | 17 % | Pilots, packaging peak start, data IA |
| 3 Scale | M7–M14 | 45 % | Waves, packaging, data migration, hypercare |
| 4 Decommission | M12–M16 | 8 % | MECM/AD FS/file/print/DC retirement |
| 5 Optimize | M15–M18 | 7 % | Optimization, handover |

## K.4 Effort for the Endpoint Desktop Engineer (individual)

| Period | Focus | Approx. allocation |
|---|---|---|
| M1–M5 | Discovery, Intune architecture, baselines, Autopilot, co-mgmt, certs with ID/NW | 100 % program |
| M5–M14 | Pilots, waves, workload moves, GPO migration, defect resolution | 90 % program / 10 % BAU escalation |
| M12–M16 | MECM decommission, cleanup, reporting migration | 70 % program |
| M15–M18 | Optimization, handover, documentation | 50 % program → BAU platform owner |

## K.5 Sensitivities (what moves the estimate most)

| Variable | Effect |
|---|---|
| App count after rationalization (±150 apps) | ±80–100 pw |
| Share count / ACL complexity | ±50–80 pw |
| Badge SSO vendor certification delayed (R-003) | +40–60 pw (extended Path D, second clinical wave) |
| Partner productivity (packaging) | ±20 % of packaging effort |
| Unplanned M&A forests | +20–40 pw per acquisition |
| E5 not approved (alternatives for Cloud PKI, EPM, PIM) | +30–50 pw (NDES build, compensating controls) |
