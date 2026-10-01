# Appendix L: Critical Success Factors

> These are the conditions that must hold for the program to succeed. Each has an owner and a test, so the Steering Committee can check them, not just admire them.

| # | Critical Success Factor | Why it's critical | How we know it's in place (test) | Owner |
|---|---|---|---|---|
| CSF-1 | **Visible, sustained executive and clinical sponsorship** | Clinical adoption and cross-department priority depend on it | CIO and CMIO attend ≥ 80 % of Steering meetings; joint messages at M1, M7, M12, M18 | ES |
| CSF-2 | **Discovery-led decisions** (≥ 60 days of authentication telemetry before waves) | Hidden dependencies cause the largest delays (R-002) | Dependency register live by M3; wave clearance gate enforced | AR |
| CSF-3 | **Cloud-native by default, with bridges that expire** | Prevents "permanent hybrid" | D-02 approved; every bridge component in §5.6 has an expiry date tracked monthly | AR |
| CSF-4 | **Certificate-based network access for Entra-joined devices proven early** | Critical path; an on-site device without Wi-Fi is unusable | M4 milestone achieved (first Entra-joined device on corporate Wi-Fi) | ID + Network |
| CSF-5 | **Clinical safety as a gate, not an afterthought** | Patient safety; loss of clinical trust ends the program | Clinical Advisory Group veto exercised as designed; 0 attributable safety events | CMIO |
| CSF-6 | **Protected program capacity** (backfill, partners) | BAU always wins without protection (R-030) | ≥ 95 % of planned FTE capacity each month | ES |
| CSF-7 | **Baseline-first configuration and config-as-code** | Avoids recreating 20 years of GPO debt; enables rollback and audit | ≤ 60 Windows profiles; Git is the source of truth; drift = 0 | EP |
| CSF-8 | **Rings and measurable gates for every change** | Limits blast radius; builds confidence | Gate scorecards used for every phase/wave; no "All devices" assignments without MAA | PM / EP |
| CSF-9 | **Data-protection controls in place before data moves** | HIPAA exposure (R-004) | Labels + DLP live before the first share migration (M5) | SE |
| CSF-10 | **KFM-verified before any wipe** | Data loss destroys trust | 0 data-loss incidents attributable to Path B | EP |
| CSF-11 | **Service Desk ready before users are touched** | Users judge the program by support quality | SD trained and ≥ 80 KB articles before P1; incident rate ≤ 3 % per wave | SD |
| CSF-12 | **Single-touch, well-communicated user events** | Minimizes fatigue (R-005) | One user event per wave per user; CSAT ≥ 4.0 | Change Lead |
| CSF-13 | **Licensing decision made early** (D-01) | E5 controls (PIM, MDE P2, MDI, Cloud PKI, EPM) shape the design | D-01 decided by M2 | ES |
| CSF-14 | **Vendor commitments secured** (EHR, badge SSO, ISE, MFPs, OEMs) | External blockers sit on the critical path | Written commitments with dates by M3; tracked as dependencies | PM / AO |
| CSF-15 | **Measured outcomes and transparent reporting** | Sustains sponsorship and funding | Executive dashboard live by M4; monthly KPI trend reviewed | PM |
| CSF-16 | **Workforce reskilling and retention** | MECM/AD experts become cloud engineers, not resistors | Certification plan funded; D-13 communicated; key-person attrition = 0 | ES + HR |
| CSF-17 | **Honest scope on AD retirement** | Credibility with leadership; avoids an over-promise | Phase 6 gated by business case (D-14) | AR |
| CSF-18 | **Security posture improves throughout, never dips** | The transition is when attackers strike (R-163) | Secure Score trend non-decreasing month over month; transitional components monitored | SE |

## The three things that will matter most

1. **Know your dependencies before you move anything.** (CSF-2, CSF-4)
2. **Protect clinical care and user trust above schedule.** (CSF-5, CSF-10, CSF-11)
3. **Let bridges expire.** Co-management, Hybrid join, AD FS, VPN, and file servers all need end dates that someone owns. (CSF-3)
