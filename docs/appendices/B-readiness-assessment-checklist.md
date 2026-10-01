# Appendix B: Readiness Assessment Checklist (Scoring Model 0–5)

> Used at M1 (baseline), every phase gate (§6.5), and before each wave. A phase can pass only if the **average is ≥ 3.5 and no domain is < 3**.

## B.1 Scoring scale

| Score | Level | General definition |
|---|---|---|
| **0** | Absent | Capability doesn't exist; no plan |
| **1** | Initial | Ad hoc; dependent on individuals; undocumented |
| **2** | Repeatable | Basic process exists; partially documented; inconsistent |
| **3** | Defined | Documented, standardized, implemented for the target scope; owners assigned |
| **4** | Managed | Measured with KPIs; monitored; exceptions governed |
| **5** | Optimized | Automated, continuously improved, proven at scale |

## B.2 Domain criteria

Score each domain by the **highest level whose criteria are all met**.

### D1. Identity & authentication

| Score | Criteria |
|---|---|
| 1 | Entra Connect syncing; AD FS federation; MFA for some users |
| 2 | MFA for all users; legacy auth partially blocked; Connect at supported version |
| 3 | Authentication methods policy; CA baseline enforced; break-glass tested; Staged Rollout planned; UPN hygiene |
| 4 | Managed authentication (no AD FS); WHfB cloud Kerberos trust deployed; phishing-resistant for admins; sign-in KPIs tracked |
| 5 | ≥ 90 % phishing-resistant usage; risk-based CA **[E5]**; automated lifecycle; Cloud Sync readiness |

### D2. Privileged access

| Score | Criteria |
|---|---|
| 1 | Standing admin rights; shared admin accounts |
| 2 | Separate admin accounts; GA count reduced |
| 3 | PIM for Entra roles **[E5]** (or minimized standing access + monitoring); PAWs for Tier 0; tiering defined |
| 4 | PIM with approval for critical roles; access reviews; Tier-0 logon restrictions on DCs |
| 5 | Zero standing privilege except break-glass; automated detection and response for privileged activity |

### D3. Device management (Windows)

| Score | Criteria |
|---|---|
| 1 | MECM only; no Intune for Windows |
| 2 | Tenant attach; co-management enabled for pilot |
| 3 | Intune baselines, compliance, RBAC, scope tags, rings; co-mgmt workloads moving; Autopilot proven |
| 4 | All workloads on Intune; Entra join ≥ 60 %; KPIs tracked; config-as-code |
| 5 | Intune-only, Entra joined (excl. signed exceptions); automated lifecycle; drift detection |

### D4. Device management (macOS, Linux, mobile, shared, kiosk)

| Score | Criteria |
|---|---|
| 1 | Mixed tools; Android device administrator; Linux unmanaged |
| 2 | Plan per platform; ABM/Managed Google Play connected |
| 3 | Each platform has defined enrollment, compliance, and config; shared device modes designed |
| 4 | All platforms enrolled and compliant ≥ 95 %; DA eliminated |
| 5 | Unified reporting; automated lifecycle |

### D5. OS servicing & patching

| Score | Criteria |
|---|---|
| 1 | WSUS/SUP manual approvals |
| 2 | ADRs; some deferral |
| 3 | WUfB/Autopatch rings defined; driver/firmware policy |
| 4 | ≥ 95 % within 14 days; expedite process; safeguard monitoring |
| 5 | Hotpatch where eligible; fully automated with release health integration |

### D6. Application management

| Score | Criteria |
|---|---|
| 1 | No inventory/ownership |
| 2 | Inventory exists; partial ownership |
| 3 | 4R rationalization; packaging standards; Win32 factory; app register with dependency profiles |
| 4 | Wave app readiness ≥ 98 %; supersedence; auto-update for top third-party apps |
| 5 | Self-service ≥ 90 %; app lifecycle automated |

### D7. Data & collaboration

| Score | Criteria |
|---|---|
| 1 | File shares dominant; no IA |
| 2 | OneDrive available; ad hoc KFM |
| 3 | IA approved; labels/DLP live; migration tooling; KFM policy; archive decision |
| 4 | Migrations proceeding with permission validation and oversharing reports |
| 5 | Shares retired; governance automated |

### D8. Security operations

| Score | Criteria |
|---|---|
| 1 | AV only; on-prem SIEM |
| 2 | MDE P1; partial Defender coverage |
| 3 | MDE P2 **[E5]**, ASR audit, Sentinel connectors, incident playbooks |
| 4 | ASR block; Sentinel primary; detections for transitional components; MTTR tracked |
| 5 | Automated response, purple-team validated |

### D9. Zero Trust controls (CA + compliance)

| Score | Criteria |
|---|---|
| 1 | MFA only for external |
| 2 | CA policies exist but no device signal |
| 3 | Compliance-based CA enforced for pilot; MAM for BYOD |
| 4 | Compliance-based CA org-wide; exclusions governed |
| 5 | Authentication strengths, token protection, auth contexts, continuous evaluation |

### D10. PKI & certificates

| Score | Criteria |
|---|---|
| 1 | AD CS autoenroll only |
| 2 | Consumer inventory |
| 3 | Cloud PKI/SCEP issuing to Entra-joined devices; ISE integrated |
| 4 | All Wi-Fi/wired/VPN via Intune-issued certs; monitoring |
| 5 | AD CS minimized; lifecycle automated |

### D11. Network readiness

| Score | Criteria |
|---|---|
| 1 | Backhauled internet; TLS inspection of Microsoft endpoints |
| 2 | Endpoints allowed; partial breakout |
| 3 | Breakout for M365 at all sites in scope; DO configured; onboarding SSID; Private Access connectors |
| 4 | Connected Cache; bandwidth monitored during waves |
| 5 | SSE/ZTNA for all remote access; VPN retired |

### D12. Monitoring & analytics

| Score | Criteria |
|---|---|
| 1 | No baseline |
| 2 | Endpoint Analytics baseline |
| 3 | Log Analytics diagnostic settings; wave tracker; technical dashboard |
| 4 | Executive dashboard automated; KPIs reviewed monthly |
| 5 | Near-real-time dashboards; anomaly alerting |

### D13. Organizational readiness (people, change, support)

| Score | Criteria |
|---|---|
| 1 | No change plan |
| 2 | Stakeholder map; comms plan draft |
| 3 | Champions/super-users recruited; SD trained; KB; training content |
| 4 | Adoption measured; hypercare model proven in pilots |
| 5 | Self-service culture; continuous improvement embedded |

### D14. Governance

| Score | Criteria |
|---|---|
| 1 | No forums |
| 2 | Steering exists |
| 3 | All forums chartered; decision log; CAB for cloud changes; RAID |
| 4 | Gate scorecards used; exception register governed |
| 5 | Policy-as-code with automated compliance evidence |

## B.3 Scoring sheet (template)

| Domain | Weight | M1 baseline (est.) | Foundations gate target | Score achieved | Evidence link | Gap actions |
|---|---|---|---|---|---|---|
| D1 Identity | 1.5 | 2 | 3 | | | |
| D2 Privileged access | 1.5 | 1 | 3 | | | |
| D3 Device mgmt (Windows) | 1.5 | 1 (Intune 0 / MECM 3) | 3 | | | |
| D4 Device mgmt (other) | 1.0 | 2 | 3 | | | |
| D5 Servicing | 1.0 | 2 | 3 | | | |
| D6 Applications | 1.0 | 2 | 3 | | | |
| D7 Data | 1.0 | 2 | 3 | | | |
| D8 SecOps | 1.0 | 2 | 3 | | | |
| D9 Zero Trust controls | 1.5 | 1 | 3 | | | |
| D10 PKI | 1.0 | 2 | 3 | | | |
| D11 Network | 1.0 | 1 | 3 | | | |
| D12 Monitoring | 0.5 | 1 | 3 | | | |
| D13 Organizational | 1.0 | 1 | 3 | | | |
| D14 Governance | 0.5 | 2 | 3 | | | |
| **Weighted average** | | **≈ 1.6** | **≥ 3.5 required, no domain < 3** | | | |

**Weighted average** = Σ(score × weight) ÷ Σ(weight). Domains weighted 1.5 are critical to Zero Trust and the critical path.

## B.4 Wave-level readiness (short form)

| # | Check | Score 0–5 | Pass ≥ |
|---|---|---|---|
| W1 | App readiness (weighted %) mapped: < 80 % = 1 … ≥ 98 % = 5 | | 4 |
| W2 | Dependency clearance (open priority ≥ 60) | | 5 (= none) |
| W3 | KFM protected % | | 4 |
| W4 | Autopilot profile assignment % | | 5 |
| W5 | SD staffing vs. plan | | 4 |
| W6 | Comms/training completion | | 3 |
| W7 | Network readiness at sites in wave | | 4 |
| W8 | Rollback readiness (spares, scripts, backups) | | 4 |
