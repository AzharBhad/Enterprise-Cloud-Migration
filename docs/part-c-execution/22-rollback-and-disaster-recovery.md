# 22. Rollback and Disaster Recovery Strategy

> **Part C: Execution** · Section 22 of 33 · Lead: Architect + Endpoint Team + Identity Team · Related: §5.5 coexistence, §21.6 severity

## 22.1 Principles

1. **Every change has a rollback plan, a rollback owner, and a tested time-to-restore (TTR).** "Roll forward" is a valid plan only if it's written down and has a TTR.
2. **Rollback triggers are pre-agreed**, so nobody debates them during an incident.
3. **Some changes are one-way doors.** Identify them early, test them more, and stage them harder (§22.3).
4. **DR for cloud services is different.** Microsoft runs the platform. Contoso is responsible for **configuration**, **identity access to the platform**, **data retention**, and **on-prem connectors**.

## 22.2 Rollback catalog

| Change | Rollback method | TTR target | Trigger | Owner |
|---|---|---|---|---|
| Co-management workload switch | Move slider back to ConfigMgr (pilot collection or all) | 1–8 h (next policy cycle) | > 5 % devices failing the workload's KPIs, or an S1 | EP |
| Intune profile / policy change | Unassign / revert to previous version from Git (Microsoft365DSC / IntuneManagement import) | 1–4 h | Profile error > 5 % or S1/S2 | EP |
| Conditional Access policy | Switch to **report-only** (pre-approved emergency change) | < 15 min | Lockout > 20 users or any clinical lockout | ID/SE |
| GPO unlink | Re-link from backup (`Restore-GPO`, relink script per OU) | 1–2 h | Missing configuration causing S1/S2 on a co-managed ring | EP/ID |
| Path B device conversion (wipe → Entra join) | **No in-place rollback.** Roll forward: re-provision as Entra join after fixing the cause; **or** swap with a spare pre-provisioned device; **or** last resort: MECM OSD to AD join (available until M12) | 30–60 min (spare swap) | Device unusable for critical work | EP/SD |
| Staged Rollout (PHS) | Remove group from Staged Rollout → back to AD FS | < 30 min (token refresh) | Auth failures > 1 % of staged group | ID |
| **Defederation** | Re-federate the domain with AD FS (kept warm for 30 days): `New-MgDomainFederationConfiguration` with the saved config | 1–2 h | Mass auth failure not fixable in 1 h | ID |
| Share cutover to SharePoint | Remove read-only ACL on source; re-map drive through Intune/GPO; reconcile files modified in SPO since cutover (Migration Manager reverse copy/manual) | 2–4 h | Business-critical process blocked | Data |
| Universal Print site cutover | Re-deploy print-server queues (kept 30 days) through GPO/Intune script | 1–2 h | Printing unavailable for a site/department | EP |
| Cloud PKI cert rollout | Wi-Fi profile issuer filter back to the AD CS cert (co-managed devices still hold it); ISE policy order revert | 1 h | Wi-Fi auth failure > 0.5 % | Network/ID |
| Autopatch ring | **Pause** quality/feature update for the ring; **rollback** feature update (within the uninstall window) | 1–4 h | Known issue / app break | EP |
| ASR rule to block | Set rule to audit / add exclusion | < 1 h | Business-critical app blocked | SE |
| Entra Private Access per-app segment | Remove app segment → fall back to VPN (kept until M14) | < 30 min | App unreachable | Network |
| AD FS decommission | **One-way door** after 30-day warm standby (see 22.3) | — | — | ID |
| MECM decommission | **One-way door** after gate (DB archived; a rebuild from backup is possible but impractical) | days | — | EP |
| DC demotion | Re-promote a new DC (rebuild); keep ≥ 2 DCs per region | hours | Unexpected consumer breaks (should have been caught by §15) | ID |

## 22.3 One-way doors

| Change | Why it's one-way | Extra safeguard |
|---|---|---|
| Path B wipe (per device) | Local data outside OneDrive is gone | KFM hard gate + local data scan + user attestation. Intune has no full-device backup, so OneDrive is the only safety net. Spare devices on hand. |
| Defederation (after AD FS decommission) | Rebuilding AD FS is a project | 30-day warm standby; 100 % staged rollout for ≥ 30 days first |
| AD FS token-signing cert revocation | Breaks any lingering RPT | Monitor zero issuance for 30 days |
| MECM site uninstall | Loss of OSD/legacy tooling | Decommission gate (§5.7); DB backup retained 12 months |
| File server deletion | Data loss | 90-day read-only; immutable archive snapshot before deletion |
| Android DA → AE re-enrollment | Factory reset | Device data on these is ephemeral by design; app config via OEMConfig |
| Disable SMS/voice MFA | Users without other methods locked out | TAP issuance process; exceptions group |
| AD retirement (Phase 6) | — | Separate business case |

## 22.4 Disaster recovery: cloud control plane

| Scenario | Impact | Preparedness | Recovery |
|---|---|---|---|
| **Accidental mass deletion / misconfiguration in Intune** (e.g., wrong wipe assignment, deleted policies) | Fleet-wide | **Multi-admin approval** for wipes, scripts, app assignments to All; RBAC; daily config export to Git | Re-import config from Git (IntuneManagement/M365DSC); restore assignments; device wipes are irreversible, which is why MAA exists |
| **Conditional Access lockout** | Tenant-wide | Break-glass accounts excluded; CA changes through report-only first; Sentinel alert on CA change | Break-glass sign-in → revert policy |
| **Compromised Global Admin** | Tenant takeover | PIM + approval, phishing-resistant MFA, PAW, CA for admin portals, Sentinel detections | IR playbook: revoke sessions, reset, review audit logs, restore config from Git |
| **Entra ID / Intune regional service incident** | Sign-ins or management degraded | Microsoft's resiliency (Entra **backup authentication service** for existing sessions); WHfB cached logon works offline; Service Health monitoring | Follow Microsoft Service Health; comms template; devices keep their last policy |
| **Entra Connect server failure** | Sync stops (new users, password changes for PHS) | Staging server ready; Connect Health alerts | Promote staging server (< 1 h) |
| **Private Network Connector failure** | On-prem app access via Private Access fails | ≥ 2 connectors per group, in 2 locations | Auto-failover; rebuild connector (30 min) |
| **Intune Certificate Connector failure** | PKCS issuance stops (S/MIME) | 2 connectors | Second connector; rebuild |
| **Cloud PKI CA issue** | New cert issuance fails | Certs valid 1 year, renewal at 20 %, so an outage has low immediate impact | Microsoft support; BYOCA chain allows re-issue |
| **Ransomware on on-prem estate** | DCs, servers encrypted | Reduced on-prem footprint; Entra-joined devices **don't depend on DCs to log on or reach M365**; immutable backups of residual AD (Azure Backup) | **Rebuild endpoints with Autopilot from the cloud** (an enormous advantage over the current state); AD forest recovery plan for residual AD |

## 22.5 Disaster recovery: endpoints and data

| Asset | Protection | RPO | RTO |
|---|---|---|---|
| User files (OneDrive/SharePoint) | Versioning, recycle bin (93 days), **Files Restore**; optional **Microsoft 365 Backup** **[ADD-ON]** for point-in-time restore of large-scale encryption/deletion events | Minutes (sync) / per M365 Backup policy | Hours |
| Device | Stateless by design: Autopilot re-provision | n/a | ≤ 1 h (user-driven) / swap |
| Intune & Entra config | Git export daily + on change | 24 h (or per change) | 4–8 h for a full config restore |
| Residual AD | Azure Backup (system state) for 2 DCs, AD forest recovery runbook, isolated recovery environment | 24 h | 24–72 h (forest recovery) |
| Archive (Azure Blob) | Immutable, GRS | — | Days (rehydration from archive tier) |
| Azure Files exceptions | Azure Backup for Files + soft delete | 24 h | Hours |

## 22.6 DR testing schedule

| Test | Frequency | First run |
|---|---|---|
| Break-glass sign-in | Quarterly | M2 |
| CA emergency rollback drill | Semi-annual | M4 |
| Intune config restore from Git (into test tenant) | Semi-annual | M6 |
| Entra Connect staging promotion | Annual | M5 |
| Device rebuild at scale (100 devices in 4 h, simulated ransomware) | Annual | M12 |
| AD forest recovery (isolated) | Annual | M14 |
| OneDrive/SharePoint mass-restore (Files Restore / M365 Backup) | Annual | M10 |

## 22.7 RACI: Rollback and DR

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Rollback catalog maintenance | I | C | **A** | R | R | R | I | C |
| Rollback decision (S1) | I (A if clinical) | R | **A** | R | R | R | C | C |
| One-way door approvals | **A** | R | R | C | C | C | C | I |
| Control-plane DR plan | I | I | **A** | R | R | R | I | I |
| DR testing | I | R | **A** | R | R | R | I | C |
