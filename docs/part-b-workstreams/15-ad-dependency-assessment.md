# 15. Active Directory Dependency Assessment

> **Part B: Workstream Plans** · Section 15 of 33 · Lead: Identity Team + Security Team · Feeds: §5.3 device paths, §11 apps, §26 risk register, Phase 6 business case

## 15.1 Why this section decides the program

Changing **devices** (Entra join), **authentication** (defederation, NTLM reduction), and **infrastructure** (DC consolidation, AD retirement) each break a *different* set of dependencies. The assessment has to answer three separate questions:

| Question | Triggered by | Example breakage |
|---|---|---|
| **Q1. What breaks when a *device* becomes Entra joined?** | Path B / Path A | App authenticates as the computer account; GPO-delivered config; machine cert from autoenrollment; scheduled task under a domain service account; script mapping drives with `%LOGONSERVER%` |
| **Q2. What breaks when *DCs move, shrink, or change*?** | DC consolidation (M12–M16) | Apps with a hard-coded DC name/IP; LDAP simple binds to a specific DC; MFPs doing LDAP address-book lookups to `dc03`; NTP pointing at a DC; DHCP/DNS hosted on a DC |
| **Q3. What breaks when *AD is retired*?** | Phase 6 | Anything using Kerberos/NTLM/LDAP against AD at all; service accounts; GPOs for servers; AD-integrated DNS; AD CS templates |

## 15.2 Data sources and collection (start M1, collect ≥ 60 days)

| # | Source | What it reveals | How to collect |
|---|---|---|---|
| 1 | **Defender for Identity** | Authentication activity per account and source device, LDAP queries, lateral movement paths, cleartext LDAP binds ("Entities exposing credentials in cleartext"), legacy protocol usage | MDI sensors on all DCs; Advanced Hunting `IdentityLogonEvents`, `IdentityQueryEvents`, `IdentityDirectoryEvents` **[E5]** |
| 2 | DC Security log: **4768/4769** (Kerberos TGT/TGS) | Which services (SPNs) get tickets, from which clients, with which encryption (RC4 = weak) | Windows Event Forwarding / AMA → Log Analytics |
| 3 | DC Security log: **4776** + NTLM operational log **8004** | NTLM authentications: client, server, account | Enable `Network security: Restrict NTLM: Audit NTLM authentication in this domain = Enable all` on DCs + `Audit incoming NTLM traffic` on servers |
| 4 | Directory Service log **2889** | **Unsigned / simple LDAP binds** with client IP and account | `HKLM\SYSTEM\CurrentControlSet\Services\NTDS\Diagnostics\16 LDAP Interface Events = 2` |
| 5 | Directory Service log **2887** | Daily count of unsigned binds | Default |
| 6 | DNS analytical/debug logs, or DNS server query logs | Clients resolving **specific DC hostnames** (hard-coding) vs. SRV records | DNS analytical logging → Log Analytics |
| 7 | Firewall / NetFlow to DC IPs | Clients connecting to DC **IPs directly** (LDAP 389/636, GC 3268/3269, Kerberos 88, RPC, SMB 445 to SYSVOL/NETLOGON) | Network team |
| 8 | SPN inventory | Kerberos services running under user/service accounts | `setspn -Q */*` or `Get-ADUser -Filter {ServicePrincipalName -like "*"}` |
| 9 | Service account usage | Where each service account logs on (4624 type 3/4/5), last logon | MDI + log analytics; `lastLogonTimestamp` |
| 10 | Endpoint scan | Scheduled tasks/services running as domain accounts, hard-coded UNC paths, `%LOGONSERVER%` usage in scripts, mapped drives | **Intune/MECM remediation script (detect-only)** reporting to Log Analytics; MECM hardware inventory extension |
| 11 | Computer-account auth | Resources whose ACLs contain **computer accounts** or groups of computers (`Domain Computers`) on shares, SQL logins (`DOMAIN\PC$`), proxy authentication | ACL scans, SQL login inventory, proxy logs |
| 12 | RADIUS/NPS/ISE | 802.1X authorization based on AD computer group membership | ISE policy export |
| 13 | AD CS | Templates, enrollment by computer/user, consumers | `certutil -view`, template ACLs |
| 14 | AD FS | Relying parties and usage | AD FS application activity report in Entra |
| 15 | Application owners | Undocumented integrations (HL7 engines, lab interfaces, PACS) | Questionnaire + interviews (§11.6 dependency profile) |
| 16 | **MFPs / scanners / medical devices** | LDAP address book lookups, scan-to-SMB with service accounts, device auth to AD | Vendor documentation + #4/#6/#7 correlation by IP |

## 15.3 Dependency taxonomy and impact matrix

| # | Dependency type | Q1 Entra-join device | Q2 DC change | Q3 AD retirement | Detection source | Remediation pattern |
|---|---|---|---|---|---|---|
| D01 | User Kerberos SSO to on-prem app/share | ⚠️ Works with **cloud Kerberos trust** / PRT-to-TGT if DC reachable | ⚠️ Needs SRV-based DC location | ❌ | 4769, MDI | Cloud Kerberos trust + Private Access; app modernization to SAML/OIDC; App Proxy with KCD |
| D02 | **Computer-account authentication** (machine context to SQL/shares/proxy) | ❌ **No AD computer object** | ✅ | ❌ | ACL/SQL scans, 4624 for `PC$` | Re-architect to user context or managed identity; keep device Hybrid (Path D) until fixed |
| D03 | NTLM authentication | ⚠️ Works for user context if DC reachable; **NTLM is deprecated** (NTLMv1 removed in Win11 24H2/Server 2025) | ⚠️ | ❌ | 4776/8004 | Enable Kerberos (SPNs, FQDN not IP), app upgrade |
| D04 | **LDAP simple bind** (cleartext) | ✅ (app-server side) | ❌ if DC renamed/removed; ❌ when LDAP signing/channel binding enforced | ❌ | 2889 | LDAPS with service account → re-point to DNS alias (`ldap.corp...`) behind load balancer → long term: Entra SCIM/Graph |
| D05 | Hard-coded DC name/IP | ✅ | ❌ | ❌ | DNS logs, firewall | DNS alias / SRV-based discovery; config change |
| D06 | GPO-delivered configuration | ❌ (no GPO on Entra join) | ✅ | ❌ | GPO reports | §14 |
| D07 | AD CS autoenrollment | ❌ | ✅ | ❌ (if AD CS retired) | Template inventory | §16 (SCEP/PKCS/Cloud PKI) |
| D08 | Scheduled tasks / services under domain service accounts on endpoints | ❌ (can't use domain credentials reliably without DC line-of-sight; no gMSA on Entra-joined) | ⚠️ | ❌ | Endpoint scan | Run as SYSTEM + cloud auth (managed identity / app registration with certificate) or move to server/Azure Automation |
| D09 | Logon scripts using `%LOGONSERVER%`, NETLOGON | ❌ | ⚠️ | ❌ | Script scan | Intune scripts/remediations |
| D10 | 802.1X authorization by AD computer group | ❌ | ✅ | ❌ | ISE policy | Cert-attribute / Intune compliance-based authorization |
| D11 | MFP LDAP address book + scan-to-SMB | ✅ | ❌ | ❌ | 2889, firewall | Scan to OneDrive/SharePoint (Universal Print scan / vendor cloud); address book through Entra/Graph connector or SMTP-only |
| D12 | Medical device service accounts (vendor-managed) | n/a | ❌ potentially | ❌ | MDI, 4624 | **Vendor engagement**. Often a permanent exception in residual AD. |
| D13 | Integrated Windows auth in IIS intranet apps | ⚠️ (Kerberos via cloud trust works; NTLM fallback issues) | ⚠️ | ❌ | IIS logs, 4769 | Entra App Proxy with KCD, or migrate to Entra auth |
| D14 | SQL Windows authentication (user) | ⚠️ (works with Kerberos via cloud trust) | ⚠️ | ❌ | SQL login audit | Entra auth for Azure SQL / SQL 2022+ Entra auth |
| D15 | AD-integrated DNS / DHCP on DCs | ✅ | ❌ | ❌ | Server roles | Decouple (§3.3) before DC reduction |
| D16 | Time (NTP hierarchy from PDC) | ✅ | ⚠️ | ❌ | Config review | Network NTP sources |
| D17 | Exchange hybrid / AD attributes as source (mail attributes) | ✅ | ✅ | ❌ | Exchange config | Remove last Exchange server management tools path (Exchange Management Tools-only) / move SOA to cloud when supported |
| D18 | HR/IAM provisioning into AD | ✅ | ✅ | ❌ | IAM config | API-driven inbound provisioning to Entra (§9) |
| D19 | Kerberos constrained delegation (KCD) for app tiers | ✅ | ✅ | ❌ | `msDS-AllowedToDelegateTo` | App Proxy KCD (still AD) → modernize |
| D20 | Linux/macOS AD binding (sssd/realmd, Jamf AD bind) | ✅ | ⚠️ | ❌ | Inventory | Platform SSO (macOS), Entra auth for Linux (where supported) / local accounts + Intune |

## 15.4 Prioritization model

Each dependency instance in the register is scored:

| Factor | Scale |
|---|---|
| **Clinical criticality** of the consuming system | 1 (none) – 5 (patient-safety critical) |
| **Blast radius** (users/devices affected) | 1 (< 10) – 5 (> 2,000) |
| **Change trigger proximity** (which wave/milestone breaks it first) | 1 (Phase 6) – 5 (next 60 days) |
| **Remediation complexity** | 1 (config change) – 5 (vendor product change) |

**Priority = Criticality × Blast radius × Proximity**, with complexity used to plan lead time. Any item with criticality 5 and proximity ≥ 4 is raised to the Steering Committee automatically.

## 15.5 Dependency register (schema)

| Field | Example |
|---|---|
| Dep-ID | DEP-0142 |
| Type (D01–D20) | D04 LDAP simple bind |
| Consumer (app/device/vendor) | Lab Information System interface engine |
| Source IP/host | 10.20.4.17 `lisint01` |
| Target | `dc03.corp.contoso-health.org` (hard-coded) |
| Account | `svc-lis-ldap` (password never expires) |
| Evidence | Event 2889 × 14,000/day |
| Breaks at | Q2 (DC03 demotion, M13) & LDAP channel binding enforcement |
| Owner | Lab IT manager |
| Remediation | Re-point to `ldaps.corp.contoso-health.org` (LB VIP), LDAPS, rotate to gMSA if supported |
| Target date / status | M10 · In progress |
| Exception? | No |

## 15.6 Standard remediation patterns

| Pattern | Applies to | Steps |
|---|---|---|
| **P-A: Cloud Kerberos trust + line of sight** | D01, D13, D14 | WHfB CKT enabled; DC reachable on-net or through Private Access (Kerberos/LDAP/DNS ports to DC segments); verify `klist` shows a TGT from the on-prem KDC after accessing the resource |
| **P-B: DNS abstraction for DCs** | D04, D05, D11 | Create `ldap.corp…` and `ldaps.corp…` VIPs behind a load balancer pointing at surviving DCs; update consumers; enables DC changes without touching apps again |
| **P-C: LDAP → LDAPS / signing** | D04 | Server certs on DCs; test with `ldp.exe`; then enforce LDAP signing + channel binding (aligns with Microsoft hardening) |
| **P-D: Modern auth uplift** | D01, D13 | SAML/OIDC through Entra Enterprise App; vendor upgrade |
| **P-E: Machine identity redesign** | D02, D08 | Replace machine-account trust with user context, certificate-based app identity (Entra app registration + cert from Cloud PKI/PKCS), or move the workload server-side |
| **P-F: Isolate** | Unfixable | AVD session host (Hybrid joined) for the app; or Path D device; time-boxed exception |
| **P-G: Vendor engagement** | D12 | Formal letter + contract clause; remains in residual AD; documented in the Phase 6 business case |

## 15.7 NTLM and legacy protocol reduction track

| Step | Month | Action |
|---|---|---|
| 1 | M1 | Audit NTLM domain-wide (no blocking) |
| 2 | M3 | Identify and eliminate **NTLMv1** (should already be absent on Win11 24H2+ clients; check servers/appliances) |
| 3 | M6 | Block NTLM for privileged accounts (Protected Users group membership for `t0-`/`t1-`) |
| 4 | M9 | Enforce **LDAP signing** and **LDAP channel binding** on DCs after the 2889 count reaches ~0 for in-scope consumers |
| 5 | M12 | Disable RC4 for Kerberos where supported (AES-only; check 4769 encryption types first) |
| 6 | M15 | NTLM blocking per server class using `Restrict NTLM: Incoming NTLM traffic = Deny all` with exception lists |
| 7 | Phase 6 | Domain-wide NTLM restriction |

## 15.8 Outputs

1. **Dependency register** (SharePoint list/Dataverse) with priority scores, wired into the RAID log.
2. **Per-wave dependency clearance report**: a wave can't start with any open priority-≥-60 dependency affecting it.
3. **DC consolidation readiness**: per-DC list of remaining direct consumers (target 0 before demotion).
4. **Phase 6 business case input**: residual dependencies, cost to remediate, cost to keep (Azure IaaS DCs + Tier-0 operations), risk.

## 15.9 RACI: AD dependency assessment

| Activity | ES | PM | AR | ID | EP | SE | AO | SD |
|---|---|---|---|---|---|---|---|---|
| Logging & telemetry enablement | I | I | C | **A/R** | C | R | I | I |
| Endpoint scans (tasks, scripts, UNC) | I | I | C | C | **A/R** | C | I | I |
| Analysis & register | I | C | **A** | R | R | R | C | I |
| App-owner validation | I | C | C | C | C | I | **A/R** | I |
| Vendor engagement (medical devices, MFPs) | C | R | C | C | C | C | **A** | I |
| Remediation execution | I | C | C | **A**/R | R | C | R | I |
| Wave clearance sign-off | I | R | **A** | C | C | C | C | I |
| Phase 6 business case | **A** | R | R | R | C | C | C | I |
