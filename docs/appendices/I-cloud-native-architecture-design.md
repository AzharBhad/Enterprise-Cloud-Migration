# Appendix I: Recommended Cloud-Native Architecture Design

> This is the reference design for the **end state**. It consolidates §3 (Future State) with the detailed component designs in Part B into one view that architects and engineers can implement.

## I.1 Reference architecture

```mermaid
flowchart TB
    subgraph Users["Personas"]
        KW[Knowledge worker]
        CLN[Clinician — shared device]
        REM[Remote / contractor]
        ADM[Privileged admin]
    end

    subgraph Endpoints["Cloud-native endpoints"]
        WIN[Windows 11 Enterprise<br/>Entra joined · Autopilot · Autopatch]
        SHR[Shared clinical PC<br/>Entra joined · SharedPC · CBA/FIDO2]
        KSK[Kiosk<br/>Self-deploying · Assigned Access · App Control]
        PAW[PAW<br/>Entra joined · App Control enforced]
        MAC[macOS<br/>ADE · Platform SSO]
        MOB[iOS / Android<br/>ADE · AE dedicated/COBO · MAM BYOD]
        CPC[Windows 365 / AVD<br/>Entra joined]
    end

    subgraph ControlPlane["Microsoft cloud control plane"]
        direction TB
        ENTRA[Entra ID<br/>CA · Auth strengths · PIM · ID Protection]
        INTUNE[Intune<br/>Config · Compliance · Apps · EPM · Cloud PKI · Remote Help]
        DEF[Defender XDR<br/>MDE · MDI · MDO · MDCA]
        PURV[Purview<br/>Labels · DLP · Audit]
        GSA[Global Secure Access<br/>Private Access · Internet Access]
        M365[M365 services<br/>Exchange · SharePoint · OneDrive · Teams · Universal Print]
        SENT[Sentinel + Log Analytics]
    end

    subgraph Azure["Azure landing zone"]
        HUB[Connectivity hub<br/>Azure Firewall · ExpressRoute ×2]
        IDS[Identity subscription<br/>DCs ×4 · Private Network Connectors ·<br/>Cert Connector · UP connector · Cloud Sync agents]
        MGT[Management subscription<br/>Log Analytics · Sentinel · Automation]
        LZ["Landing zones<br/>AVD host pools · Azure Files (Entra Kerberos) ·<br/>Archive Blob (immutable)"]
    end

    subgraph OnPrem["Residual on-prem (minimized)"]
        EHR[EHR back-end · PACS · LIS]
        MED["Medical devices (vendor-managed)"]
        ISE[Cisco ISE · Wi-Fi/wired 802.1X]
        DC2["DCs ×2 (until Phase 6)"]
    end

    Users --> Endpoints
    Endpoints -->|PRT, tokens| ENTRA
    Endpoints -->|MDM/IME| INTUNE
    Endpoints -->|sensor| DEF
    Endpoints --> M365
    Endpoints -->|GSA client| GSA --> IDS --> EHR
    INTUNE -->|compliance| ENTRA
    DEF -->|risk| INTUNE
    ENTRA & INTUNE & DEF & PURV --> SENT
    Endpoints -->|EAP-TLS cert| ISE
    INTUNE -. "Cloud PKI SCEP" .-> Endpoints
    HUB --- OnPrem
    IDS --- DC2
    LZ --> CPC
```

## I.2 Design decisions summary (ADR index)

| ADR | Decision | Rationale | Section |
|---|---|---|---|
| ADR-01 | Entra join for all new and converted Windows devices | MS guidance; removes AD dependency; works anywhere | §5.4 |
| ADR-02 | Co-management as time-boxed bridge; MECM decommissioned M15 | Low-risk transition, clear exit | §5.7, §18 |
| ADR-03 | Managed authentication (PHS); AD FS retired | Security, simplicity, Cloud Sync path | §9 |
| ADR-04 | WHfB **cloud Kerberos trust** | No PKI dependency; on-prem SSO | §9 |
| ADR-05 | Cloud PKI (BYOCA) for authentication certs; PKCS only for S/MIME | Lowest ops; key stays on device | §16 |
| ADR-06 | Windows Autopatch for all servicing | Single servicing model; ring automation | §10.4 |
| ADR-07 | OneDrive KFM + SharePoint/Teams; Azure Files for app exceptions | Collaboration + governance; app compatibility | §12 |
| ADR-08 | Universal Print except EHR clinical printing | Server retirement; clinical safety | §12.7 |
| ADR-09 | Entra Private Access for private apps; VPN only for exceptions | ZTNA with per-app CA | §13.3.5 |
| ADR-10 | Sentinel as SIEM | Native integration; consolidated SOC | §13.6 |
| ADR-11 | AVD for clinical pooled VDI; W365 for contractors (gated) | Cost/fit per persona | §10.3.4 |
| ADR-12 | Intune for macOS with Platform SSO (unless D-08 says otherwise) | Single console | §10.3.5 |
| ADR-13 | Configuration as code (Git) for Intune/Entra | Change control, rollback, audit | §31.5 |
| ADR-14 | Residual AD in Azure IaaS (Tier 0); retirement gated | Honest scope for healthcare dependencies | §3.7, §15 |

## I.3 Non-functional requirements

| NFR | Requirement | Design response |
|---|---|---|
| Availability | Sign-in & device management available ≥ 99.9 % (Microsoft SLA) | Microsoft cloud SLAs; WHfB cached logon; backup authentication service |
| Resilience (on-prem dependencies) | No single point of failure in connectors | ≥ 2 Private Network Connectors / Cert Connectors / UP connectors / Cloud Sync agents in 2 locations |
| Recoverability | Endpoint RTO ≤ 1 h; config RTO ≤ 8 h | Autopilot re-provisioning; Git config restore |
| Security | Phishing-resistant MFA; compliant device for corporate data; least privilege | CA, compliance, PIM, EPM, LAPS |
| Compliance | HIPAA/HITRUST/PCI evidence on demand | Log Analytics/Sentinel retention; automated evidence exports |
| Performance | Provisioning ≤ 45 min; badge tap → EHR ≤ 30 s; boot-to-desktop −35 % | Thin ESP, DO/MCC, shared PC tuning, Endpoint Analytics |
| Scalability | 20,000+ endpoints; M&A onboarding in days | Cloud services; Cloud Sync for disconnected forests; Autopilot |
| Data residency | US | Tenant/regions US |

## I.4 Naming and tagging standards (excerpt)

| Object | Standard | Example |
|---|---|---|
| Windows device (assigned) | `CH-%SERIAL%` (≤ 15 chars) | `CH-5CG1234XYZ` |
| Windows device (shared/kiosk) | `CH-SH-%RAND:6%` / `CH-KS-%RAND:6%` | `CH-SH-4K9Q2M` |
| Intune policy | `<PLAT>-<TYPE>-<SCOPE>-<Desc>-v<n>` | `WIN-SC-ALL-Edge-Hardening-v2` |
| Entra group (device, dynamic) | `GRP-DEV-<Plat>-<Purpose>` | `GRP-DEV-WIN-Ring3` |
| Entra group (user) | `GRP-USR-<Purpose>` | `GRP-USR-EPM-Physicians` |
| CA policy | `CA<nnn>-<Persona>-<App>-<Control>` | `CA105-SharedClinical-All-CBA` |
| Autopilot group tag | Persona code | `KW`, `SHC`, `KSK`, `VIP`, `PAW` |
| Azure resources | CAF naming + tags (`CostCenter`, `Workload`, `Env`, `Program`) | `vm-dc-eus2-01` |
