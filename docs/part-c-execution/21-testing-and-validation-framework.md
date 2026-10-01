# 21. Testing and Validation Framework

> **Part C: Execution** · Section 21 of 33 · Lead: Architect (test authority) + Endpoint Team · Related: §11.7 app testing, §19 pilots, §20 go/no-go

## 21.1 Test levels

| Level | Purpose | Environment | Entry | Exit | Owner |
|---|---|---|---|---|---|
| **T1 Unit / config** | Each Intune profile, CA policy, app package works as designed | **Test tenant** + Ring 0 lab | Config peer-reviewed (PR) | Profile success; expected registry/CSP values verified | EP / ID |
| **T2 Integration** | Components work together: Autopilot + ESP + certs + Wi-Fi + WHfB + Kerberos + Private Access + apps | Ring 0 (production tenant, lab devices) | T1 pass | End-to-end scenario scripts pass (§21.3) | EP + ID + Network |
| **T3 Persona / UAT** | Real users run their real work | P1/P2/P3 pilots | T2 pass | Pilot exit criteria (§19.5) | AO + Change |
| **T4 Performance** | Provisioning time, logon time, EHR launch, bandwidth impact | Pilot sites, clinical sim lab | T2 pass | Within baseline thresholds | EP + Clinical informatics |
| **T5 Security** | CA bypass attempts, compliance evasion, LAPS/EPM abuse, token theft scenarios, ASR effectiveness | Test tenant + Ring 0 | T2 pass | No critical findings; residual risk accepted | SE (+ external pen test M9, M17) |
| **T6 Operational acceptance (OAT)** | Runbooks, monitoring, alerting, SD procedures, DR | Production | Before each phase gate | Runbook dry-run success; alerts fire | SD + EP |
| **T7 Regression (continuous)** | Monthly: Windows quality update, Intune service release (`YYMM`), app updates, CA changes don't break critical scenarios | Ring 0 → Ring 1 | Every change | Automated smoke suite green | EP |

## 21.2 Test environments

| Environment | Purpose | Notes |
|---|---|---|
| **Test tenant** (E5 trial/dev) | Destructive and policy-design testing: CA, compliance, auth methods, PIM | Config exported from production through Microsoft365DSC to keep parity |
| **Ring 0 production lab** | 30 devices, every hardware model, Hybrid + Entra joined, real network (wired/Wi-Fi/remote) | Production tenant; isolated groups |
| **Clinical simulation lab** | Replica nursing station: badge reader, label printer, scanner, signature pad, WoW (workstation on wheels) | Clinical informatics owns the test scripts |
| **Network test bench** | ISE test policy, onboarding SSID, Private Access connector | Network team |

## 21.3 End-to-end scenario test catalog (T2)

| ID | Scenario | Expected result |
|---|---|---|
| E2E-01 | New device, user-driven Autopilot, home network | Desktop ≤ 45 min; compliant; apps; WHfB prompt |
| E2E-02 | Path B: co-managed Hybrid device → Intune wipe → Entra join, on corporate Wi-Fi | KFM content present; Wi-Fi cert issued; MECM client absent; old Hybrid object removed after 14 days |
| E2E-03 | Pre-provisioned at depot → shipped → user phase on clinic Wi-Fi | User phase ≤ 15 min |
| E2E-04 | Self-deploying kiosk | Assigned Access app running; no user prompt |
| E2E-05 | WHfB sign-in → access `\\fs01\dept$` → Kerberos ticket from on-prem KDC | `klist` shows cifs/fs01 ticket; no password prompt |
| E2E-06 | Same as E2E-05 off-network through Entra Private Access | Access works; CA106 applied |
| E2E-07 | Legacy IWA intranet app (IIS) from Entra-joined device | SSO through Kerberos |
| E2E-08 | Badge tap on shared clinical device → EHR ready | ≤ 30 s; correct user context; logoff/tap-over works |
| E2E-09 | Non-compliant device (BitLocker off) tries Outlook | Blocked with remediation message; becomes compliant → access within 15 min |
| E2E-10 | BYOD iPhone Outlook without MAM | Blocked; with MAM → allowed, copy/paste to personal apps blocked |
| E2E-11 | User risk high (simulated) **[E5]** | Blocked / forced password change |
| E2E-12 | Print to Universal Print from Entra-joined device; secure release | Job prints; audit trail |
| E2E-13 | Scan from MFP to OneDrive | File appears |
| E2E-14 | LAPS retrieval by SD Tier 2 | Password retrieved, audited, rotated post-use |
| E2E-15 | EPM elevation (support-approved) **[E5]** | Request → approval → elevation logged |
| E2E-16 | Remote Help session with elevation | Session logged; compliance warning shown for non-compliant |
| E2E-17 | Autopilot reset of Entra-joined device | Device returns to user-ready state, enrollment kept |
| E2E-18 | Lost device: retire/wipe + BitLocker key rotation + Autopilot dereg on disposal | Completed per SOP |
| E2E-19 | Autopatch quality update (Ring 0) + expedite | Installed within deadline; restart respects active hours |
| E2E-20 | Feature update 24H2 → 25H2 via Autopatch | Success; apps work |
| E2E-21 | Break-glass sign-in | Succeeds; Sentinel alert fires within 5 min |
| E2E-22 | macOS ADE + Platform SSO + Kerberos SSO to file share | SSO works |
| E2E-23 | Zebra dedicated device, shared device mode sign-in/out | Session cleared between users |
| E2E-24 | Defederated sign-in (post-M10) for every persona | No AD FS dependency; sign-in logs show managed auth |

## 21.4 Automation

| Tool | Use |
|---|---|
| **Pester** tests run on Ring 0 devices (scheduled) | Validate expected settings (registry/CSP), services, certs, apps installed, Kerberos tickets |
| **Microsoft Graph** scripts | Validate assignment coverage, profile success %, compliance %, CA report-only impact |
| **Maester** (open-source, Graph-based) / **CA what-if** | Conditional Access and Entra configuration regression tests |
| **Microsoft365DSC** compare | Drift detection: production vs. Git baseline |
| Endpoint Analytics | Startup/app reliability regression vs. baseline |

## 21.5 Validation checkpoints (per device after migration)

The `Post-Migration-Health` remediation script reports to Log Analytics:

| Check | Pass condition |
|---|---|
| Join type | `AzureAdJoined : YES`, `DomainJoined : NO` (Path A/B) |
| PRT | `AzureAdPrt : YES` |
| Kerberos cloud trust | `OnPremTgt : YES` / `CloudTgt : YES` (`dsregcmd /status`) |
| MDM | Enrolled, last sync < 8 h |
| Compliance | Compliant |
| BitLocker | On, key escrowed |
| LAPS | Password backed up to Entra |
| Defender | Active, EDR onboarded, tamper protection on |
| Certificates | Cloud PKI device cert present, valid > 30 days |
| OneDrive | Signed in, KFM protected, no sync errors |
| MECM client | Absent (post-M13) |
| Apps | Wave required apps installed |

## 21.6 Defect severity model

| Severity | Definition | Response | Wave impact |
|---|---|---|---|
| **S1 Critical** | Clinical safety impact, data loss, security breach, or > 100 users unable to work | Immediate; war room; rollback considered | **Stop** current wave |
| **S2 High** | A department or critical app is unusable; no workaround | 4 h workaround, 2 days fix | Pause next tranche |
| **S3 Medium** | Degraded function with a workaround | Fix within wave | Continue |
| **S4 Low** | Cosmetic / minor | Backlog | Continue |

## 21.7 RACI: Testing and validation

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Test strategy & environments | I | C | **A** | C | R | C | I | I |
| T1/T2 execution | I | I | C | R | **A/R** | R | I | I |
| T3 UAT | I | C | C | I | R | I | **A/R** | C |
| T4 performance | I | I | C | I | **A/R** | I | R | I |
| T5 security testing | I | I | C | C | C | **A/R** | I | I |
| T6 OAT | I | C | C | C | R | C | I | **A/R** |
| T7 regression automation | I | I | C | C | **A/R** | C | I | I |
