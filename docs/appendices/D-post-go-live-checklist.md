# Appendix D: Post-Go-Live Checklist

> Used after **each wave** (T+1 to T+90) and after **program completion**.

## D.1 Wave: first 24 hours (T+1)

| # | Check | Target | Owner | ✓ |
|---|---|---|---|---|
| 1 | Devices converted vs. plan | ≥ 95 % | EP | ☐ |
| 2 | Autopilot first-attempt success | ≥ 95 % | EP | ☐ |
| 3 | Devices compliant | ≥ 95 % (24 h) | EP | ☐ |
| 4 | Post-migration health script: all checks pass (join, PRT, cloud TGT, MDM, BitLocker, LAPS, Defender, cert, OneDrive, apps) | ≥ 95 % | EP | ☐ |
| 5 | Wi-Fi/wired auth success at wave sites | ≥ 99.5 % | Network | ☐ |
| 6 | Users signed in with WHfB / registered passkeys | ≥ 70 % day 1 | ID | ☐ |
| 7 | OneDrive sync healthy, no KFM errors | ≥ 98 % | EP | ☐ |
| 8 | Printing working (UP jobs succeeding) | ≥ 98 % | EP | ☐ |
| 9 | Incidents logged in wave category; top 5 issues identified with workarounds | — | SD | ☐ |
| 10 | No clinical safety events | 0 | Clinical | ☐ |

## D.2 Wave: hypercare (T+2 to T+10)

- [ ] Daily stand-up held; issue burn-down published
- [ ] Incident rate trending to ≤ 1.2× pre-wave baseline
- [ ] KB updated with new resolutions (within 24 h of a pattern)
- [ ] Remaining non-compliant devices remediated or excluded with reason
- [ ] Spare devices returned/re-provisioned
- [ ] Pulse survey (C8) sent; CSAT ≥ 4.0
- [ ] Hypercare exit criteria met (§20.6), PM sign-off

## D.3 Wave: clean-up (T+10 to T+90)

- [ ] Stale **Hybrid** Entra device objects removed (after 14 days)
- [ ] AD computer objects disabled → deleted per policy
- [ ] MECM records removed for converted devices
- [ ] Legacy share access attempts monitored, trending to 0 (read-only period)
- [ ] Share archive snapshot taken at T+90; share retired; DFS-N links updated/removed
- [ ] Print queues for wave sites removed after 30 days
- [ ] GPOs fully unlinked from wave OUs and logged in the GPO burn-down
- [ ] Lessons learned incorporated into the next wave's plan

## D.4 Program completion (M18) checklist

### Technical
- [ ] Entra-joined share ≥ 85 % (100 % excl. signed exceptions)
- [ ] All Path D exceptions reviewed; signed by CMIO/CISO with review dates
- [ ] MECM, AD FS, DirectAccess, print servers, file servers retired; DCs ≤ 6
- [ ] Autopatch steady state; quality update currency ≥ 95 %
- [ ] Compliance ≥ 97 %; Secure Score ≥ 75 %
- [ ] Token/certificate expiry monitoring active (APNs, ADE, VPP, connectors)
- [ ] Drift detection operational; config-as-code is the source of truth

### Security & compliance
- [ ] CA exclusions minimized and governed
- [ ] HIPAA risk analysis updated (M12 refresh complete)
- [ ] Compliance evidence pack automated (§28.4)
- [ ] Purple-team findings remediated
- [ ] Legacy SIEM and EDR retired; Sentinel retention per policy

### Operations
- [ ] BAU support model running ≥ 60 days with SLAs met (§32)
- [ ] Runbooks, KB, dashboards accepted by BAU owners
- [ ] DR tests per schedule completed (§22.6)
- [ ] Training for new joiners (IT) in place

### Business
- [ ] KPIs at target, or variance plans approved (§4.2)
- [ ] Benefits register handed to Finance
- [ ] Lessons learned published
- [ ] Phase 6 (AD retirement) decision recorded (D-14)
- [ ] Program formally closed by Steering Committee
