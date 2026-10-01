# Risk Analysis, Part 2: Legacy Blockers, Security, Business Continuity, and User Experience

> **Risk Analysis** · IDs **R-150 to R-199** · Scoring per §26.1

## RA-3. Legacy systems that can block the migration

| ID | Legacy system / pattern | Why it blocks | Impact | Prob. | Severity | Mitigation | Contingency |
|---|---|---|---|---|---|---|---|
| R-150 | **Windows 10 devices on unsupported hardware** (~900) | Can't run Win11 → can't be cloud-native under the program's OS standard | ESU cost, security exposure | H | High | Accelerated replacement; ESU Year 2 | Isolate; restrict access via CA |
| R-151 | **Embedded / IoT Windows (LTSC) devices**: lab instruments, imaging workstations, nurse-call consoles | Vendor-controlled OS; no Intune changes allowed; often AD-joined | Can't be moved; need AD | H | High | Classify as "vendor-managed exception"; network segmentation; MDE where vendor allows | Keep in residual AD; Phase 6 business case input |
| R-152 | **Windows Server-based "desktops"** (users logging on to servers) and **RDS farms** | Not in Intune scope | Shadow endpoints persist | M | Medium | Move users to AVD; servers to Arc | Retain RDS short-term |
| R-153 | **32-bit / 16-bit apps, apps needing old .NET/Java versions** | Compatibility on Win11 24H2/25H2 | App outage | M | High | App Assure; AVD/Citrix isolation | Keep Win10 ESU VM in AVD for the app (time-boxed) |
| R-154 | **Apps requiring IE11 engine beyond IE mode** (ActiveX controls needing specific doc modes, Java applets) | IE mode supports ActiveX/document modes, but some legacy plugin and Java runtime combinations are problematic and Java itself is a security liability. Test each one. | App outage | M | High | IE mode with document mode testing; vendor upgrade | Isolated legacy browser on AVD (restricted) |
| R-155 | **App-V packages** | App-V client not carried forward | Repackaging effort | H | Medium | Re-package as Win32/MSIX in the factory | Keep App-V on Path D devices (temporary) |
| R-156 | **Citrix with on-prem AD-joined VDAs & StoreFront domain pass-through** | Entra-joined endpoints need Citrix Workspace SSO via Entra (FAS / SAML) | EHR access sign-in prompts | H | High | Citrix FAS + Entra SAML on Citrix Gateway/Workspace; test in P0 | Users type credentials temporarily |
| R-157 | **Tap-badge SSO vendor (e.g., Imprivata) version not supporting Entra join** | Requires upgrade/new architecture | Shared clinical stays Hybrid | M | High | Vendor roadmap + upgrade budget | Path D |
| R-158 | **Legacy MDM on Android (device administrator)** | Must factory reset to move to Android Enterprise | Disruption to supply chain devices | H | Medium | Scheduled re-enrollment by department with spare devices | Phased |
| R-159 | **Jamf on-prem with AD-bound Macs** | AD bind breaks when moving to Platform SSO | Mac sign-in issues | M | Medium | Unbind as part of migration; Platform SSO; local account password sync | Keep AD bind temporarily |
| R-160 | **On-prem Exchange servers for SMTP relay / hybrid management** | Apps relay through on-prem Exchange | Mail from apps/MFPs stops | M | High | Inventory relays; EXO connectors; SMTP AUTH alternatives (high-volume email, Graph sendMail) | Keep relay server |
| R-161 | **Old PKI templates (SHA-1, 1024-bit keys) still trusted by apps/devices** | Cloud PKI/modern certs differ | Appliance auth failures | L | Medium | Template audit | Retain legacy issuance for appliances |
| R-162 | **SQL Server reporting/BI tied to Windows integrated auth & Kerberos delegation** | Double-hop across tiers | Report failures from Entra-joined clients | M | Medium | Test E2E-07 variant; KCD config; Entra auth for SQL 2022+ | AVD access |

## RA-4. Security risks during transition

| ID | Risk | Impact | Prob. | Severity | Mitigation | Contingency |
|---|---|---|---|---|---|---|
| R-163 | **Dual attack surface**: old (AD FS, AD, MECM, VPN) and new (Entra, Intune) both live; attackers target the weaker | Breach | M | **Critical** | Harden transitional components (MDI, AD FS extranet lockout, Tier-0 PAW-only); retire on schedule (§5.6) | IR plan specific to transitional components |
| R-164 | **Over-broad CA exclusions** created to unblock pilots, never removed | Persistent bypass of MFA/compliance | H | High | Exclusion groups with expiry, owner, quarterly access review; Sentinel alert on membership change | Emergency cleanup sweep |
| R-165 | **Intune admin compromise** (wipe/script push to the fleet) | Fleet-wide destruction / malware deployment | L | **Critical** | PIM, multi-admin approval (scripts, wipes, apps), PAW, phishing-resistant CA, RBAC scope tags, Sentinel alerts | Config restore from Git; Autopilot rebuild |
| R-166 | **Token theft / AiTM phishing** against M365 sessions | Mailbox/data compromise | M | High | Phishing-resistant MFA, token protection, CAE, compliant device requirement, MDO P2 **[E5]** | Revoke sessions, ID Protection auto-remediation |
| R-167 | **LAPS password retrieval abuse** | Lateral movement | L | Medium | RBAC for LAPS read, audit + Sentinel alert on bulk retrieval, post-auth reset | Rotate all LAPS passwords (Intune action) |
| R-168 | **Standing local admin during transition** (pilot users granted admin "temporarily") | Malware persistence | M | Medium | EPM from day 1; local admin removal policy | Remove via Local user group membership policy |
| R-169 | **Unmanaged devices accessing M365 during the bridge** | Data exfiltration | H | High | CA102/CA103 (MAM, web restrictions) before waves | Block unmanaged access org-wide |
| R-170 | **Sync of privileged on-prem accounts to Entra** (or cloud admin roles on synced accounts) | Cloud compromise via on-prem | M | High | Exclude Tier-0 OUs from sync; cloud-only admins; Entra Connect server as Tier-0 | Remove roles; re-scope sync |
| R-171 | **Entra Connect / Cloud Sync servers inadequately protected** | Full tenant compromise path (PHS secrets) | L | **Critical** | Tier-0 treatment, MDI sensor, restricted logon, patching, no internet browsing | IR; rotate `AZUREADSSOACC` and sync account credentials |
| R-172 | **Security baselines conflicting with clinical needs** (screen lock, USB block) leading to ad-hoc exceptions | Policy drift | H | Medium | Clinical persona design with CISO + CMIO; documented deviations | Exception register |
| R-173 | **Weak MFA methods retained "temporarily"** (SMS) | AiTM / SIM-swap | M | High | Retirement date (M9); exception group expiry | Force re-registration campaigns |

## RA-5. Business continuity risks

| ID | Risk | Impact | Prob. | Severity | Mitigation | Contingency |
|---|---|---|---|---|---|---|
| R-174 | **Clinical unit loses EHR access during device swap** | Patient care delay | L | **Critical** | Tranche swaps by room/pod; spare devices; downtime viewer; avoid shift change | EHR downtime procedures; immediate device swap |
| R-175 | **Wi-Fi authentication failure for Entra-joined devices** (ISE policy error) | Site-wide connectivity loss for migrated devices | M | **Critical** | Staged ISE changes; monitoring of auth success; dual cert during transition | ISE policy rollback; onboarding SSID temporary access |
| R-176 | **Mass Autopilot failure on wave day** (service incident, ESP app failure) | Users without devices | M | High | Thin ESP; check Service Health before T-0; tranche-based wipes (not all at once) | Halt remaining tranches; spare swaps |
| R-177 | **Defederation weekend failure** | Org-wide sign-in failure | L | **Critical** | Staged rollout 100 % first; rehearsal in test tenant; warm AD FS | Re-federate (§22.2) |
| R-178 | **Entra ID outage** | Sign-in to cloud apps degraded | L | High | Backup authentication service, WHfB cached logon, EHR on-prem/Citrix unaffected; continuity comms | Downtime procedures |
| R-179 | **Print outage for clinical documentation (labels, wristbands)** | Patient identification risk | L | **Critical** | Keep EHR clinical print path out of Universal Print scope; test before touching | Restore print queue; manual labels per downtime |
| R-180 | **Share cutover during finance close / payroll** | Financial reporting delay | M | High | Blackout calendar; finance cutover window chosen with CFO office | Re-enable source share |
| R-181 | **Ransomware during transition** exploiting incomplete controls | Major outage | M | **Critical** | Prioritize MDE P2 + ASR + LAPS + PIM early (M3–M8); immutable backups | Cloud rebuild of endpoints via Autopilot; AD forest recovery |

## RA-6. User experience risks

| ID | Risk | Impact | Prob. | Severity | Mitigation | Contingency |
|---|---|---|---|---|---|---|
| R-182 | **Slower first sign-in on shared clinical devices** (profile creation for each new user) | Clinician frustration, workarounds | H | High | Shared PC settings; pre-warm profiles via vendor tooling; disable WHfB provisioning on shared devices; measure | Stay Path D until fixed |
| R-183 | **OneDrive Files On-Demand confusion** ("where are my files?", offline access) | Tickets, lost productivity | H | Medium | Training U2; "Always keep on this device" for critical folders; clinic offline guidance | Floor-walkers |
| R-184 | **Loss of drive letters** breaks muscle memory | Tickets | H | Medium | Shortcuts in File Explorer; Teams pinning; communications | Temporary Azure Files drive mapping |
| R-185 | **Different device names / new devices** confuse users and SD | Tickets | M | Low | Device name in Company Portal, desktop wallpaper/BGInfo-like info via remediation | — |
| R-186 | **Notifications overload** (Company Portal, WHfB, OneDrive, Autopatch restarts) | Users ignore critical prompts | H | Medium | Notification design review; Autopatch restart notifications aligned to active hours | Communication |
| R-187 | **EPM elevation delays** for physicians | Workflow interruptions | M | Medium | Pre-approved rules; 15-min SLA; physician champion feedback | Temporary user-confirmed rules |
| R-188 | **Performance regression from security stack** (MDE + ASR + DLP + GSA client) on older hardware | Slow devices | M | Medium | Endpoint Analytics baseline vs. post; exclusions tuned; hardware refresh targeting | Hardware replacement |
| R-189 | **Remote workers with poor home bandwidth during Path B** | Long rebuild time, re-download of OneDrive | M | Medium | Schedule outside work hours; Files On-Demand; pre-provisioned replacement shipped instead of wipe | Device swap by courier |
