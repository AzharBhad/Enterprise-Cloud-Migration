# Risk Analysis (Deep Dive)

This section goes deeper than the program [Risk Register (§26)](../part-d-risk-governance/26-risk-register.md). It covers the risks that usually derail enterprise cloud migrations, and each one is given Impact, Probability (H/M/L), Severity (Critical/High/Medium/Low), Mitigation, and a Contingency Plan.

| Part | Topics | IDs |
|---|---|---|
| [Part 1: Forgotten items & hidden dependencies](01-forgotten-items-and-hidden-dependencies.md) | What organizations commonly forget; DC-hard-coded apps, LDAP binds, Kerberos, NTLM, SMB, scanners/MFPs, scheduled tasks under service accounts; discovery checklist | R-101 to R-140 |
| [Part 2: Legacy blockers, security, continuity, user experience](02-legacy-security-continuity-ux.md) | Legacy systems that block migration; transition-period security risks; business continuity; UX | R-150 to R-189 |
| [Part 3: Regulatory, third-party, technical debt, downtime](03-compliance-thirdparty-techdebt-downtime.md) | Compliance risks; vendor/integration impacts; technical debt; 15 downtime scenarios with a wave-day decision tree | R-200 to R-230, DT-01 to DT-15 |

## Top 10 across the deep dive (by severity × proximity)

| Rank | ID | Risk | Severity | First exposure |
|---|---|---|---|---|
| 1 | R-121 / R-122 | DC-hard-coded apps and LDAP simple binds | Critical | DC consolidation (M12) / LDAP signing enforcement (M9) |
| 2 | R-125 | Computer-account-based authorization | Critical | First Path B wave (M8) |
| 3 | R-175 / R-212 | Wi-Fi authentication failure for Entra-joined devices (ISE) | Critical | IT pilot (M5) |
| 4 | R-163 | Dual attack surface during the bridge | Critical | M1–M15 |
| 5 | R-174 / DT-02 | Clinical unit EHR access during device swap | Critical | Clinical pilot (M7) |
| 6 | R-179 | Clinical label/wristband printing | Critical | Universal Print rollout (M6+) |
| 7 | R-133 | Password-vaulting SSO breaks with passwordless | High | Clinical pilot (M7) |
| 8 | R-126 / R-127 | Apps and MFPs writing to SMB shares | High | First share cutover (M7) |
| 9 | R-182 | Slow first sign-in on shared clinical devices | High | Clinical pilot (M7) |
| 10 | R-206 | Part 11 validated workstations changed without validation | High | Wave 6 (M11) |

All R-1xx/R-2xx risks are entered in the RAID log (App-G) with owners at M2, and reviewed in the weekly RAID review (§27.3).
