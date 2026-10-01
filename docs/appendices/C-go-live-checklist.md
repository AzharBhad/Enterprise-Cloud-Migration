# Appendix C: Go-Live Checklist

> Used for **each wave go-live (T-2 days to T-0)** and for **major cut-overs** (defederation, CA enforcement, MECM decommission). Sign-off columns are required. The checklist is a gate, not a formality.

## C.1 Wave go-live: T-2 days (go/no-go meeting)

| # | Item | Evidence | Owner | ✓ |
|---|---|---|---|---|
| 1 | Previous wave hypercare exit met (incident rate ≤ 3 %, 0 open P1/P2) | Wave closure report | PM | ☐ |
| 2 | Wave app readiness ≥ 98 % weighted; 100 % clinical-critical | App register report | EP | ☐ |
| 3 | 0 open priority ≥ 60 dependencies for this wave | Dependency register | ID | ☐ |
| 4 | KFM protected ≥ 98 % of Path B devices; failures excluded | KFM report | EP | ☐ |
| 5 | Autopilot profile `Assigned` for 100 % of Path B devices | Intune export | EP | ☐ |
| 6 | Cert/Wi-Fi profiles assigned; ISE policy verified for wave sites | ISE test log | Network | ☐ |
| 7 | Universal Print printers for wave sites registered and assigned | UP portal | EP | ☐ |
| 8 | Share migration: final delta scheduled; read-only scripts tested | Migration plan | Data | ☐ |
| 9 | GPO unlink/security filter prepared for wave OU/group; backups taken | GPO backup log | EP/ID | ☐ |
| 10 | Spare devices staged (≥ 3 % of wave; 10 % for clinical) | Asset list | SD | ☐ |
| 11 | SD staffing confirmed (3 % incident rate + 20 % buffer); floor-walkers rostered | Roster | SD | ☐ |
| 12 | Comms C1–C4 sent; training completion ≥ 60 % | Comms log | Change | ☐ |
| 13 | No blackout conflict (finance close, clinical events, EHR change, holidays) | Enterprise change calendar | PM | ☐ |
| 14 | Microsoft Service Health clear; no active Sev-1 security incident | Service Health / SOC | SE | ☐ |
| 15 | Rollback owners named and reachable; rollback runbooks reviewed | Rollback catalog | AR | ☐ |
| 16 | Clinical waves: CMIO/CNO delegate approval; unit downtime procedures reviewed | Sign-off | Clinical | ☐ |

**Decision:** ☐ Go ☐ Go with conditions (list) ☐ No-go. Vetoes: Clinical lead, CISO, SD lead.

## C.2 Wave go-live: T-0 (day-of runbook)

| Time | Step | Owner | ✓ |
|---|---|---|---|
| T-0 −2 h | War room/bridge open; dashboards up (wave tracker, Autopilot, compliance, ISE auth, ITSM queue) | PM | ☐ |
| T-0 −1 h | Service Health check; final KFM check; go/no-go re-confirmation (5 min) | EP | ☐ |
| T-0 | Share cutover: source read-only → final delta → drive mapping removed → links published | Data | ☐ |
| T-0 | GPO unlink/filter applied for wave | EP | ☐ |
| T-0 +15 min | Path B tranche 1 (≤ 10 % of wave) wipes approved (MAA) and issued | EP | ☐ |
| T-0 +90 min | Tranche 1 checkpoint: Autopilot success ≥ 95 %, no S1/S2 → proceed | EP | ☐ |
| Rolling | Tranches 2…n at agreed cadence; Path A handouts/shipments | EP/SD | ☐ |
| Hourly | Status to stakeholders (Teams); issues log updated | PM | ☐ |
| EOD | Day summary: devices converted, success %, incidents, open issues, next-day plan | PM | ☐ |

**Stop conditions (any one):** clinical impact · > 50 users affected by one issue · Autopilot success < 90 % for a tranche · Wi-Fi auth failure > 0.5 % at a site · S1 security incident.

## C.3 Major cut-over checklists

### C.3.1 Defederation (M10)
- [ ] 100 % Staged Rollout for ≥ 30 days; failure delta < 0.1 %
- [ ] All RPTs migrated or confirmed retired; AD FS issuance only from expected sources
- [ ] PHS healthy (Connect Health); Seamless SSO key rolled within 30 days
- [ ] Federation config exported and stored (for rollback)
- [ ] Rehearsed in test tenant
- [ ] Cut-over window (Sunday 02:00), comms to SD; CA and sign-in log monitoring live
- [ ] Rollback decision point at +1 h; AD FS kept warm 30 days

### C.3.2 CA "require compliant device" org-wide (M11)
- [ ] Report-only ≥ 14 days with impact < 0.5 % of sign-ins
- [ ] Non-compliant device remediation campaign done
- [ ] Exclusion groups reviewed (owner, expiry)
- [ ] Emergency revert procedure tested; break-glass verified

### C.3.3 MECM decommission (M15)
- [ ] Gate criteria §5.7 #1–#6 evidenced
- [ ] Consumers of MECM reports signed off
- [ ] DB backup to immutable storage verified (restore test)
- [ ] Site uninstall plan reviewed by Design Authority

## C.4 Sign-off

| Role | Name | Decision | Date |
|---|---|---|---|
| Program Manager | | | |
| Architect | | | |
| Endpoint lead | | | |
| Service Desk lead | | | |
| Security (CISO delegate) | | | |
| Business owner of wave | | | |
| Clinical lead (clinical waves) | | | |
