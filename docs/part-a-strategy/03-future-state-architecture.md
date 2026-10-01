# 3. Future State Architecture

> **Part A: Strategy** · Section 3 of 33 · Detailed design per workstream is in Part B (§9–§18). The reference design is in Appendix I.

## 3.1 Architecture principles

| # | Principle | Implication | Basis |
|---|---|---|---|
| P1 | **Entra ID is the identity control plane** | Every user, device, app, and workload identity authenticates to Entra ID. AD DS becomes a downstream, legacy-compatibility directory. | [MS-BP] |
| P2 | **Cloud-native endpoints by default** | New Windows devices are Entra joined and Autopilot provisioned. Hybrid join exists only as a time-boxed exception with an exit date. | [MS-BP] |
| P3 | **Verify explicitly, least privilege, assume breach** | Conditional Access on every resource. Device compliance is a grant control. PIM for every privileged role. EPM instead of local admin. | Zero Trust [MS-BP] |
| P4 | **Configuration as code, single source of truth** | Intune configuration is exported and version-controlled (Microsoft Graph, Microsoft365DSC or IntuneManagement). Changes go through PR-reviewed change. | [OPINION] |
| P5 | **Phishing-resistant authentication** | WHfB, FIDO2/passkeys, and certificate-based authentication. SMS/voice are retired as MFA methods. | [MS-BP], HIPAA risk analysis |
| P6 | **No new on-prem dependencies** | Any new app or service must support SAML/OIDC + SCIM, or be published through Entra Private Access / App Proxy. | Governance |
| P7 | **Clinical safety over speed** | Clinical workflows get their own rings, pilots, rollback, and blackout windows. | Healthcare-specific |
| P8 | **Measure everything** | Every workstream reports KPIs into a Log Analytics workspace and Power BI. Go/no-go decisions are data-driven. | [OPINION] |

## 3.2 Logical architecture (end state, M18)

```mermaid
flowchart TB
    subgraph Identity["Identity & Access Plane — Microsoft Entra"]
        EID[Microsoft Entra ID<br/>P1 all / P2 E5 users]
        CA[Conditional Access<br/>+ Authentication Strengths]
        PIM[PIM / PAM<br/>E5]
        IDP[ID Protection<br/>E5]
        GSA[Global Secure Access<br/>Entra Private + Internet Access]
        CSYNC[Entra Cloud Sync<br/>+ Connect Sync until hybrid devices = 0]
        EAPP[Enterprise Apps<br/>SAML/OIDC + SCIM]
    end

    subgraph Endpoint["Endpoint Management Plane — Microsoft Intune"]
        INT[Intune<br/>Plan 1 + E3/E5 advanced]
        AP[Windows Autopilot<br/>+ Device Preparation]
        AUTO[Windows Autopatch]
        EPM[Endpoint Privilege Mgmt<br/>E5]
        CPKI[Microsoft Cloud PKI<br/>E5]
        RH[Remote Help<br/>E3+]
        EA[Endpoint Analytics<br/>+ Advanced Analytics]
    end

    subgraph Security["Security Plane — Microsoft Defender XDR"]
        MDE[Defender for Endpoint P2<br/>E5]
        MDI[Defender for Identity<br/>E5]
        MDO[Defender for Office 365 P2]
        MDCA[Defender for Cloud Apps]
        PUR[Purview: DLP, Labels,<br/>Audit Premium]
        SENT[Microsoft Sentinel<br/>+ Log Analytics]
    end

    subgraph Data["Data & Collaboration Plane — Microsoft 365"]
        OD[OneDrive<br/>KFM]
        SPO[SharePoint Online<br/>dept/project sites]
        TEAMS[Teams]
        AZF[Azure Files<br/>Entra Kerberos — exceptions]
        UP[Universal Print]
    end

    subgraph Devices["Endpoints (~18,300)"]
        W[Windows 11<br/>Entra joined]
        SH[Shared clinical<br/>Entra joined + shared PC]
        K[Kiosk / signage<br/>Assigned Access]
        VDI[AVD / Windows 365<br/>Entra joined]
        MAC[macOS<br/>Intune + Platform SSO]
        LNX[Linux<br/>Intune + MDE]
        IOS[iOS/iPadOS<br/>ADE + shared iPad / MAM]
        AND[Android Enterprise<br/>dedicated / WP / COPE]
    end

    subgraph Residual["Residual On-Prem / Azure IaaS (minimized)"]
        RAD[(AD DS — 4 DCs<br/>Azure IaaS, Tier-0)]
        LEG[Legacy Kerberos/LDAP apps<br/>EHR back-end, lab, PACS]
        CONN[Private Network Connectors<br/>Cert Connector, Cloud Sync agents]
    end

    Devices -- "Enroll / policy / apps" --> INT
    Devices -- "Sign-in (WHfB/FIDO2/PRT)" --> EID
    EID --> CA
    CA -- "device compliance signal" --> INT
    CA -- "risk signal" --> IDP
    INT --> MDE
    MDE & MDI & MDO & MDCA --> SENT
    Devices --> OD & SPO & TEAMS & UP
    Devices -- "Kerberos via cloud Kerberos trust" --> GSA --> CONN --> LEG
    LEG --> RAD
    CSYNC -- sync --> RAD
    CSYNC --> EID
    CPKI -. "SCEP certs (Wi-Fi/VPN)" .-> Devices
    AUTO -. "WUfB rings / drivers" .-> W
```

## 3.3 Component decisions (summary)

The full rationale and decision frameworks are in Part B.

| Capability | Today | Future state | Transitional (bridge) | Licensing note | Section |
|---|---|---|---|---|---|
| User sign-in authority | AD FS federation | **Managed auth: PHS + Seamless SSO (bridge) → PRT-based SSO** | Staged Rollout from AD FS | P1 | §9 |
| Sync | Entra Connect Sync | **Entra Cloud Sync** for users/groups. **Connect Sync retained only while Hybrid-joined devices exist** (Cloud Sync doesn't sync device objects). | Connect Sync ≥ 2.6.x | — | §9 |
| Strong auth | Password + SMS | **WHfB (cloud Kerberos trust)**, FIDO2, passkeys (Authenticator), **CBA** for shared clinical (badge/smart card) | Number-matching Authenticator | P1 (Auth Strengths) | §9, §13 |
| Device identity | AD joined | **Entra joined** | Hybrid Entra joined + co-managed | — | §10, §17 |
| Windows management | MECM | **Intune** | Co-management, workload by workload | E3 | §10, §18 |
| Provisioning | PXE / OSD | **Autopilot** (user-driven, pre-provisioned, self-deploying). **Autopilot device preparation** for simple user-driven profiles. | MECM OSD for in-life rebuilds until M12 | E3 | §17 |
| Patching | SUP / WSUS | **Windows Autopatch** (+ WUfB reports, driver & firmware mgmt) | Co-mgmt Windows Update workload | E3 (Autopatch included in E3/E5) | §10 |
| App delivery | MECM apps | **Intune Win32**, Enterprise App Catalog **[E5]**, Microsoft Store (winget), M365 Apps | MECM apps through co-mgmt "Client apps" workload | EAM is E5 | §11 |
| Privilege | Local admin + legacy LAPS | **Windows LAPS → Entra ID**, **EPM [E5]** | — | EPM: E5 | §13 |
| Certificates | AD CS autoenroll | **Cloud PKI [E5]** for device/user auth certs. **AD CS + Intune Certificate Connector (PKCS)** for S/MIME and other cases needing key archival. | NDES/SCEP fallback if not E5 | Cloud PKI: E5 | §16 |
| Wi-Fi auth | EAP-TLS via ISE with AD lookup | **EAP-TLS with Intune-issued cert**. ISE authorizes by cert attributes / Intune compliance API. | Dual SSIDs during transition | — | §16 |
| Remote access | Cisco VPN + DirectAccess | **Entra Private Access (ZTNA)**. VPN retained only for unsupported protocols. | VPN with Entra SAML + device cert | Entra Suite / Private Access SKU **[ADD-ON]** | §13 |
| User data | H: drives / Folder Redirection | **OneDrive + KFM** | Dual availability during cutover | — | §12 |
| Department data | DFS-N shares | **SharePoint/Teams**. Exceptions on **Azure Files**. Cold data to archive. | Read-only shares post-migration | — | §12 |
| Print | 14 print servers | **Universal Print** (native / connector). EHR clinical label printing stays on the EHR print path. | Universal Print connector on 2 VMs | UP included in E3/E5 (job quota) | §12 |
| Remote support | MECM Remote Control | **Intune Remote Help [E3+]** | — | E3+ | §32 |
| VDI | Citrix on-prem | **Azure Virtual Desktop** (multi-session, Entra joined) for clinical. **Windows 365** for contractors/third parties. | Citrix retained until EHR vendor certifies AVD | Azure consumption [ADD-ON] | §10 |
| macOS | Jamf on-prem | **Intune + Platform SSO (Entra)** **[OPINION]**, or Jamf Pro Cloud + Intune compliance partner if research requires Jamf | Jamf + Entra integration | — | §10 |
| SIEM | 3rd-party on-prem | **Microsoft Sentinel** (Defender XDR connector, Entra, Intune diagnostic logs) | Forward to existing SIEM during overlap | Sentinel consumption [ADD-ON]; E5 data grant | §13 |
| DNS / DHCP | Windows on DCs | **Azure DNS Private Resolver + on-prem DNS appliances** (Infoblox/BlueCat or Windows on non-DC servers). DHCP on network infrastructure. | AD DNS retained for residual AD | — | §15 |

## 3.4 Identity and access flow (end state)

```mermaid
sequenceDiagram
    autonumber
    participant U as Clinician
    participant D as Entra-joined Windows 11
    participant E as Entra ID
    participant CA as Conditional Access
    participant I as Intune
    participant APP as M365 / SaaS app
    participant GSA as Entra Private Access
    participant K as Legacy Kerberos app (on-prem)

    U->>D: Tap badge / WHfB PIN / FIDO2
    D->>E: Request PRT (+ partial TGT via cloud Kerberos trust)
    E-->>D: PRT + Kerberos partial TGT
    U->>APP: Open app
    APP->>E: Token request
    E->>CA: Evaluate (user, device, app, risk, location)
    CA->>I: Is device compliant?
    I-->>CA: Compliant (BitLocker, MDE risk ≤ low, OS ≥ min)
    CA-->>E: Grant (auth strength: phishing-resistant)
    E-->>APP: Access token
    U->>K: Open legacy app (\\ehr-int.corp...)
    D->>GSA: Traffic captured by GSA client
    GSA->>E: CA evaluation for Private Access app
    D->>K: Kerberos service ticket (full TGT obtained from DC through connector)
    K-->>U: Access granted (SSO)
```

## 3.5 Network architecture shift

| Aspect | Today | Future |
|---|---|---|
| Internet egress | Centralized backhaul from 42 clinics | **Local breakout for M365 "Optimize" and "Allow" endpoints** at all sites (SD-WAN). Entra Internet Access or existing SSE for web filtering. |
| Content delivery | 38 MECM DPs | **Delivery Optimization** (peer-to-peer, `DOGroupIdSource` = AD site → Entra tenant/subnet), **Microsoft Connected Cache** on 6–8 hub nodes (hospitals and the largest clinics) |
| Private app access | Full-tunnel VPN | **Entra Private Access** per-app segments, connectors in datacenter and Azure |
| Azure connectivity | 1 Gbps ExpressRoute (dev/test) | **Azure Landing Zone (hub-spoke or Virtual WAN)**, ExpressRoute 2× for resiliency, Azure Firewall |
| 802.1X | ISE + AD lookup | ISE + **certificate attributes** + Intune **compliance check through Graph** (ISE ↔ Intune integration uses the Compliance Retrieval API; validate the ISE version supports it) **[ASSUMPTION ASM-04]** |

## 3.6 Management plane & admin model

| Element | Design |
|---|---|
| Admin workstations | **Privileged Access Workstations (PAW)**: Entra joined, Intune managed, Defender-hardened, separate admin accounts (cloud-only `adm-` accounts, no mailbox) |
| Admin roles | Entra built-in roles + **Intune RBAC custom roles** scoped with **scope tags** (by region/site and by platform); **PIM [E5]** for all eligible activation with approval for Global Admin, Intune Admin, Conditional Access Admin |
| Emergency access | 2 **break-glass** cloud-only accounts, FIDO2-protected, excluded from CA, monitored in Sentinel |
| Change control | Intune and CA changes through Git-backed config (Microsoft365DSC export daily), **multi-admin approval** in Intune for scripts, apps, and wipe actions **[MS-BP]** |
| Tenant | Single production tenant. **Separate test tenant** (Microsoft 365 E5 developer/trial or purchased) for CA and Intune policy validation |

## 3.7 What stays on-premises (and why)

| Component | Why it stays | Exit condition |
|---|---|---|
| AD DS (4 DCs in Azure IaaS + 2 in primary DC until Phase 6) | Kerberos/LDAP for EHR back-end, lab systems, PACS, medical device service accounts | All residual apps either modernized, moved to Entra Domain Services, or accepted as permanent exception |
| Entra Connect Sync | Device writeback/sync for Hybrid-joined shared clinical workstations until they convert | Hybrid-joined device count = 0 → migrate to Cloud Sync |
| Intune Certificate Connector (PKCS) | S/MIME with key archival | Business decision on S/MIME vs. Purview Message Encryption |
| Private Network Connectors | Entra Private Access / App Proxy | Permanent (connectors are lightweight, stateless) |
| Clinical label printing via EHR print services | EHR vendor requirement | EHR vendor roadmap |
| Citrix (until certified) | EHR vendor certification of AVD/W365 | Vendor certification + clinical pilot |
