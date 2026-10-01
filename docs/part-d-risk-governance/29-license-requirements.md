# 29. License Requirements

> **Part D: Risk, Compliance, Governance** · Section 29 of 33 · Owner: Architect + Licensing/Procurement · Decision: **D-01**
>
> Licensing reflects Microsoft packaging as documented in **October 2026**, including the **July 2026 distribution of Intune Suite capabilities into Microsoft 365 E3/E5**. Microsoft changes packaging often. **Validate every line with your Microsoft account team / LSP before you commit budget.** This section deliberately gives **no list prices**, because EA pricing, discounts, and promotions vary too much.

## 29.1 Capability → license matrix

| Capability used in this blueprint | M365 E3 | M365 E5 | M365 F3 | Notes / add-on path |
|---|---|---|---|---|
| Intune Plan 1 (MDM/MAM, compliance, Autopilot, Win32, settings catalog) | ✅ | ✅ | ✅ | |
| Intune **Plan 2** (specialty devices, Tunnel for MAM, FOTA) | ✅ (from Jul 2026) | ✅ | ❌ | F3: Intune Suite / Plan 2 add-on |
| **Remote Help** | ✅ (from Jul 2026) | ✅ | ❌ | F3 users are mostly *sharers*. Licensing is per helper/sharer; **validate F3 sharers** with Microsoft |
| **Advanced Analytics** (Endpoint Analytics advanced) | ✅ (from Jul 2026) | ✅ | ❌ | |
| **Endpoint Privilege Management** | ❌ | ✅ (from Jul 2026) | ❌ | E3: EPM standalone / Intune Suite |
| **Microsoft Cloud PKI** | ❌ | ✅ (from Jul 2026) | ❌ | E3: standalone / Suite; alternative NDES or third-party |
| **Enterprise App Management** | ❌ | ✅ (from Jul 2026) | ❌ | E3: standalone / Suite; alternative Store/winget |
| Windows Enterprise E3 (Autopatch, Credential Guard, App Control, WUfB driver mgmt) | ✅ | ✅ (E5) | ✅ (E3) | Autopatch included with Windows Enterprise E3+ |
| Entra ID **P1** (CA, dynamic groups, MDM auto-enroll) | ✅ | ✅ | ✅ | |
| Entra ID **P2** (PIM, ID Protection risk-based CA, access reviews) | ❌ | ✅ | ❌ | P2 add-on or E5 Security add-on |
| Entra ID Governance (Lifecycle Workflows advanced, entitlement mgmt advanced) | ❌ | Partial (P2 features) | ❌ | Entra ID Governance add-on for full Lifecycle Workflows |
| **Entra Private Access / Internet Access** | ❌ | ❌ | ❌ | **Entra Suite** or standalone **[ADD-ON]** |
| Defender for Endpoint **P1** | ✅ | — (P2 included) | ❌ | F3: **Microsoft Defender Suite FLW** (P2) or MDE standalone. **Shared clinical devices used only by F3 users have no MDE coverage without this.** |
| Defender for Endpoint **P2** (EDR, AIR, Vulnerability Mgmt) | ❌ | ✅ | ❌ | E5 Security / F5 Security add-on |
| **Defender for Identity** | ❌ | ✅ | ❌ | E5 Security / F5 Security / standalone |
| Defender for Office 365 **P1** | ✅ (**from 1 Jul 2026**) | ✅ (P2) | ❌ | |
| Defender for Office 365 **P2** | ❌ | ✅ | ❌ | E5 Security / Defender Suite add-on |
| **Defender for Cloud Apps** | ❌ | ✅ | ❌ | E5 Security add-on |
| Purview sensitivity labels (manual), basic DLP | ✅ | ✅ | ✅ (limited) | |
| Purview auto-labeling, Endpoint DLP, Teams DLP, Audit (Premium), Insider Risk | ❌ | ✅ | ❌ | E5 Compliance / F5 Compliance add-on |
| Universal Print | ✅ (pooled jobs) | ✅ | ✅ | Additional job volume add-on |
| OneDrive / SharePoint / Teams | ✅ | ✅ | ✅ (2 GB OneDrive, no desktop apps) | F3 = web/mobile Office apps only |
| Microsoft 365 Apps (desktop) | ✅ | ✅ | ❌ | Shared clinical devices: **shared computer activation** needs a license that includes desktop apps for each user who signs in. Validate F3 users on shared PCs (F3 = no desktop apps, so web apps or E3 for those users) |
| AVD access rights (Windows client OS) | ✅ | ✅ | ✅ | + Azure consumption |
| **Windows 365** Cloud PC | ❌ | ❌ | ❌ | Per-user **[ADD-ON]** |
| Microsoft Sentinel | — | Data grant for some M365 E5 sources (check current terms) | — | Azure consumption **[ADD-ON]** |
| Microsoft 365 Backup | ❌ | ❌ | ❌ | Consumption **[ADD-ON]** |
| SharePoint Advanced Management | ❌ | ❌ | ❌ | Add-on (or included with some Copilot licensing; validate) |
| Windows 10 **ESU Year 2** | ❌ | ❌ | ❌ | Per-device **[ADD-ON]** |
| Security Copilot (incl. Copilot in Intune) | ❌ | Microsoft announced inclusion for E5 customers; **verify current entitlement and capacity** | ❌ | SCU capacity otherwise **[ADD-ON]** |

## 29.2 Persona-based licensing recommendation (D-01)

| Persona | Count | Recommended license | Rationale | Alternative (if E5 not approved) |
|---|---|---|---|---|
| Knowledge workers | 7,300 | **Microsoft 365 E5** | PIM/ID Protection, MDE P2, MDI coverage, EPM, Cloud PKI, Endpoint DLP, Audit Premium all directly mitigate R-013/R-017/R-004 | **E3 + E5 Security add-on** (gets Entra P2, MDE P2, MDI, MDO P2, MDCA). Cloud PKI/EPM then need Intune Suite or standalone add-ons, or alternatives (NDES, no EPM) |
| IT / privileged admins | 220 (in KW count) | **E5** (mandatory) | Privileged accounts are the highest risk | Minimum: Entra ID P2 for all admin identities |
| Clinical frontline (shared devices) | 1,900 | **F3 + Microsoft Defender Suite FLW** (frontline security add-on, formerly sold as F5 Security; confirm the current SKU name) (+ the frontline Purview/compliance add-on if PHI DLP is needed on endpoints) | Frontline economics; shared devices need MDE P2 coverage (device-based) and ID Protection for users | F3 only + device-level compensating controls. **Check desktop Office need on shared PCs.** |
| Contractors / affiliates (corporate device) | 600 | **E3** (+ E5 Security if privileged/PHI-heavy) | Full desktop needed | — |
| Contractors / affiliates (own device, VDI) | 500 | **F3 or E3 + Windows 365 Enterprise** | Isolates unmanaged devices from PHI | AVD pooled (lower cost, more ops) |
| Service/shared mailboxes, kiosks | ~450 kiosk devices | **No user license for kiosks without user sign-in**; Intune **device-only** licensing where applicable | Self-deploying kiosks with no user affinity | — |
| Shared clinical iPhones (1,200) and Zebra (900) | 2,100 devices | Covered by the signed-in user's license (shared device mode); dedicated devices without users → **Intune device license** | | |

## 29.3 Licensing assumptions to confirm

| # | Assumption | Why it matters |
|---|---|---|
| LIC-1 | The tenant has been provisioned with the July 2026 E3/E5 Intune capability changes (Message Center notice) | Remote Help/Advanced Analytics/EPM/Cloud PKI availability |
| LIC-2 | F3 users on shared Windows workstations don't need desktop M365 Apps (EHR via Citrix/AVD; web Office sufficient) | Otherwise those users need E3 or an F3 → E3 step-up |
| LIC-3 | Defender coverage on shared devices: MDE is licensed **per user** (up to 5 devices each). **F3 includes no MDE plan**, so devices used only by F3 users need the frontline Defender add-on or MDE device licensing. | Coverage gap risk: legacy EDR ends M9 |
| LIC-4 | ESU Year 2 quantity = residual Win10 devices not replaced/upgraded by Oct 2026 | Cost avoidance |
| LIC-5 | Universal Print pooled job allowance is sufficient (estimate from print server logs) | Add-on volume |
| LIC-6 | Entra Private Access user count (all remote/hybrid users, eventually all users) | Add-on cost |
| LIC-7 | Security Copilot entitlement terms for E5 | Budget for SCUs |
| LIC-8 | Sentinel ingestion volume estimate (Defender XDR raw + Entra + Intune + DC security events) | Consumption budget |

## 29.4 Licensing timeline

| When | Action |
|---|---|
| M1 week 1 | ESU Year 2; MDI trial/licensing for discovery (or E5 Security trial) |
| M2 | D-01 decision; true-up/step-up order (E3 → E5 step-up SKUs) aligned to EA anniversary if possible |
| M3 | Entra P2 for all admin identities (if broader E5 is delayed) |
| M4 | Cloud PKI (production, HSM-backed. **Don't take trial CAs to production**) |
| M5 | Entra Private Access licensing for pilot → scale with waves |
| M9 | Windows 365 / AVD consumption (D-10) |
| M9 | Legacy EDR contract ends (savings) |
| M15+ | Retire licensing for MECM-adjacent third-party tools, remote-support tool, SMS MFA gateway, on-prem Jamf |

## 29.5 License governance

- **Group-based licensing** in Entra (license groups per persona). Monitor license assignment errors.
- **Monthly reconciliation**: assigned vs. active users (inactive > 60 days → review), device counts for device-licensed SKUs.
- **Persona drift**: move users between E5/E3/F3 through HR attribute-driven dynamic groups.
- **Avoid shelfware**: track feature adoption for E5 capabilities (PIM activations, MDI alerts handled, EPM elevations, DLP matches) in the executive dashboard.
