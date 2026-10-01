# Appendix H: Recommended Microsoft Best Practices

> These are consolidated recommendations that reflect Microsoft's published guidance (Microsoft Learn, Zero Trust guidance, Intune/Autopilot/Entra documentation) as of October 2026, plus field-proven practice. Items marked **[MS-BP]** are explicit Microsoft guidance. Items marked **[Field]** are practitioner consensus.

## H.1 Identity (Microsoft Entra)

| # | Practice | Basis |
|---|---|---|
| 1 | Use **managed authentication** (PHS, with Seamless SSO/PRT) and retire AD FS where possible; use **Staged Rollout** to migrate | [MS-BP] |
| 2 | Keep **two cloud-only break-glass accounts**, excluded from CA, with phishing-resistant credentials and monitoring | [MS-BP] |
| 3 | **Never sync on-prem privileged accounts**, and never assign cloud admin roles to synced accounts; use cloud-only admin accounts | [MS-BP] |
| 4 | Use **PIM** for just-in-time privileged access with approval for the most critical roles | [MS-BP] **[E5/P2]** |
| 5 | Move to the **Authentication methods policy**; prefer **phishing-resistant** methods (WHfB, FIDO2, passkeys, CBA); enforce with **Authentication strengths** | [MS-BP] |
| 6 | Deploy **WHfB with cloud Kerberos trust** for hybrid environments (no PKI dependency) | [MS-BP] |
| 7 | **Block legacy authentication**; block device code flow except where required | [MS-BP] |
| 8 | Build CA policies in **report-only** first; use the **CA insights workbook** and **what-if** | [MS-BP] |
| 9 | Treat **Entra Connect / Cloud Sync servers as Tier 0**; keep Connect at a supported version; evaluate **Cloud Sync** | [MS-BP] |
| 10 | Use **groups and exclusions with owners and expiry**; review with **access reviews** | [MS-BP] / [Field] |

## H.2 Endpoint management (Intune, Autopilot, Autopatch)

| # | Practice | Basis |
|---|---|---|
| 11 | **Deploy new devices as Entra joined (cloud-native)**; Hybrid join via Autopilot isn't recommended | [MS-BP] |
| 12 | Use **co-management** to move workloads progressively; switch **Compliance** first | [MS-BP] |
| 13 | Use **Group Policy Analytics** to assess GPOs; **migrate to settings catalog**; start from **security baselines** | [MS-BP] |
| 14 | Prefer the **settings catalog** over templates and custom OMA-URI | [MS-BP] |
| 15 | Use **assignment filters** for targeting conditions instead of creating many groups | [MS-BP] |
| 16 | Use **scope tags + custom RBAC roles** for least-privilege administration | [MS-BP] |
| 17 | Enable **multi-admin approval** for high-impact actions (scripts, app assignments, wipes) | [MS-BP] |
| 18 | Set the tenant compliance setting **"Mark devices with no compliance policy as Not compliant"** | [MS-BP] |
| 19 | **Keep ESP minimal**; avoid mixing MSI LOB and Win32 apps during Autopilot | [MS-BP] |
| 20 | Register devices through **OEM/reseller**; use **group tags** for profile targeting | [MS-BP] |
| 21 | Use **Windows Autopatch** for quality, feature, driver/firmware, M365 Apps, Edge, and Teams updates with ring-based deployment | [MS-BP] |
| 22 | Back up **BitLocker recovery keys to Entra ID before encryption**; use **Windows LAPS** with Entra backup | [MS-BP] |
| 23 | Use **Endpoint Privilege Management** instead of standing local admin | [MS-BP] **[E5]** |
| 24 | Configure **Delivery Optimization** and **Microsoft Connected Cache** for content at scale | [MS-BP] |
| 25 | Don't TLS-inspect Intune, Autopilot, WNS, or Entra authentication endpoints; follow published endpoint lists | [MS-BP] |
| 26 | Keep **Apple tokens** (APNs, ADE, VPP) and **Android Enterprise** bindings owned by shared accounts, with renewal reminders | [MS-BP] / [Field] |
| 27 | Move off **Android device administrator** to Android Enterprise | [MS-BP] |
| 28 | Export configuration regularly and manage it as code | [Field] |

## H.3 Security (Defender, Zero Trust)

| # | Practice | Basis |
|---|---|---|
| 29 | Connect **MDE to Intune**; use MDE **device risk** in compliance policies | [MS-BP] |
| 30 | Enable **tamper protection** tenant-wide; **EDR in block mode** | [MS-BP] |
| 31 | Deploy **ASR rules** in audit, analyze, then block; start with the "standard protection" rules | [MS-BP] |
| 32 | Run **Defender for Identity** on all DCs, AD FS, AD CS, and Entra Connect servers | [MS-BP] **[E5]** |
| 33 | Use **Privileged Access Workstations** and an **enterprise access model** (tiering) | [MS-BP] |
| 34 | Plan for **NTLM deprecation**: audit, reduce, block; enforce **LDAP signing and channel binding** | [MS-BP] |
| 35 | Apply **sensitivity labels** and **DLP** before migrating data into SharePoint/OneDrive | [MS-BP] / [Field] |
| 36 | Use **MAM (App Protection Policies)** for BYOD with CA "require app protection policy" | [MS-BP] |

## H.4 Data and collaboration

| # | Practice | Basis |
|---|---|---|
| 37 | Use **Known Folder Move** (silent) with throttled rollout (≤ 1,000 existing devices/day); for folders redirected to network shares, pre-copy with **Migration Manager**, then disable Folder Redirection, then enable KFM | [MS-BP] |
| 38 | Use **Migration Manager** for file share migrations; scan first; migrate with incremental passes | [MS-BP] |
| 39 | Restrict sync to corporate tenants (`AllowTenantList`) and block personal accounts | [MS-BP] |
| 40 | Design a **flat, hub-based** SharePoint information architecture; avoid replicating deep folder trees | [MS-BP] |
| 41 | Use **Azure Files with Entra Kerberos** for SMB workloads that can't move to SharePoint; exclude its app from MFA CA policies (unsupported) | [MS-BP] |

## H.5 Certificates

| # | Practice | Basis |
|---|---|---|
| 42 | Use **SCEP** (Cloud PKI or NDES) for authentication certs (private key never leaves the device); use **PKCS** only where key recovery is needed | [MS-BP] |
| 43 | Use **TPM key storage** for Windows SCEP certs where supported | [MS-BP] |
| 44 | Publish CRL/AIA over **HTTP** reachable off-network | [MS-BP] |
| 45 | Don't take **trial Cloud PKI CAs** (software keys) into production | [MS-BP] |

## H.6 Operations and governance

| # | Practice | Basis |
|---|---|---|
| 46 | Monitor **Service Health** and **Message Center**; review the **Intune What's New** monthly | [MS-BP] |
| 47 | Send Intune and Entra logs to **Log Analytics**; use **Sentinel** for detection | [MS-BP] |
| 48 | Use **Endpoint Analytics** to baseline and improve experience; use **Remediations** proactively | [MS-BP] |
| 49 | Use **Microsoft FastTrack** for deployment guidance where eligible | [MS-BP] |
| 50 | Run **ring-based** deployment for every change (policy, app, update) | [MS-BP] / [Field] |
