# Project 01 — Live SOC Alert Monitoring

**Status:** 🔄 In Progress  
**Platform:** LetsDefend  
**Started:** October 1, 2026  
**Goal:** Triage 10 live SOC alerts and document each one with structured investigation notes, IOC extraction, and MITRE ATT&CK mappings.

---

## 🎯 Objective

Learn the real-world SOC alert triage workflow:
- Receive alert → investigate → correlate evidence → classify → document
- Build the analyst mindset: "what is the evidence telling me?"
- This is the **#1 daily task** of a SOC L1 analyst

---

## 🧰 Tools Used

| Tool | Purpose |
|------|---------|
| LetsDefend | SOC simulation platform — alert queue, investigation channel, playbooks |
| VirusTotal | URL, IP, file hash reputation lookups |
| AbuseIPDB | IP address abuse history |
| urlscan.io | URL/domain analysis |
| MITRE ATT&CK | Threat technique mapping |
| Log Management (LetsDefend) | Internal log correlation (Basic + Pro views) |

---

## 📋 Alert Progress

| # | Alert | Severity | Verdict | Date | Log |
|---|-------|----------|---------|------|-----|
| 1 | SOC101 - Phishing Mail Detected (EventID 87) | Medium | ✅ True Positive | 2026-10-02 | [log](./triage-log.md#alert-1--soc101-phishing-mail-detected) |
| 2 | SOC205 - Malicious Macro Executed (EventID 231) | High | ✅ True Positive | 2026-10-03 | [log](./triage-log.md#alert-2--soc205-malicious-macro-has-been-executed) |
| 3 | SOC325 - Unauthorized Cloud Region Access (EventID 303) | Low | ✅ True Positive | 2026-10-04 | [log](./triage-log.md#alert-3--soc325-unauthorized-cloud-region-access-attempt) |
| 4 | SOC276 - Account Discovery Attempt (EventID 251) | **Critical** | ✅ True Positive | 2026-10-06 | [log](./triage-log.md#alert-4--soc276-account-discovery-attempt-detected) |
| 5 | SOC312 - Unauthorized Template Modification (EventID 290) | **Critical** | ✅ True Positive | 2026-10-07 | [log](./triage-log.md#alert-5--soc312-unauthorized-template-modification-detected) |
| 6 | *(pending)* | | | | |
| 7 | *(pending)* | | | | |
| 8 | *(pending)* | | | | |
| 9 | *(pending)* | | | | |
| 10 | *(pending)* | | | | |

**Progress:** 5 / 10 alerts triaged (50%)

---

## 📊 Stats

| Metric | Value |
|--------|-------|
| Total Alerts Triaged | 5 |
| True Positives | 5 |
| False Positives | 0 |
| Benign | 0 |
| Critical Severity Confirmed | 2 |
| Confirmed Compromises | 2 |
| Average Time per Alert | ~2 hours (learning pace) |
| MITRE Techniques Mapped | 22 |
| IOCs Extracted | 15 |

---

## 🧭 MITRE ATT&CK Techniques Covered

| Tactic | Technique | ID | Alert |
|--------|-----------|-----|-------|
| Initial Access | Phishing: Spearphishing Link | T1566.002 | #1 |
| Initial Access | Phishing: Spearphishing Attachment | T1566.001 | #2 |
| Execution | PowerShell | T1059.001 | #2 |
| Execution | Windows Command Shell | T1059.003 | #2 |
| Execution | User Execution: Malicious File | T1204.002 | #2 |
| Command & Control | Application Layer Protocol | T1071 | #2 |
| Command & Control | Ingress Tool Transfer | T1105 | #2 |
| Command & Control | Non-Standard Port | T1571 | #2 |
| Resource Development | Compromise Accounts | T1586 | #3 |
| Defense Evasion | Valid Accounts | T1078 | #3, #4, #5 |
| Initial Access | External Remote Services | T1133 | #3, #4, #5 |
| Defense Evasion | Unused/Unsupported Cloud Regions | T1535 | #3 |
| Execution | Unix Shell | T1059.004 | #4 |
| Credential Access | Brute Force | T1110 | #4, #5 |
| Discovery | Account Discovery | T1087 | #4 |
| Persistence | Office Template Macros | T1137.001 | #5 |
| Defense Evasion | Template Injection | T1221 | #5 |
| Execution | Visual Basic | T1059.005 | #5 |
| Command & Control | Web Protocols | T1071.001 | #5 |

---

## 🎯 IOCs Extracted

| # | IOC | Type | Context | Alert |
|---|-----|------|---------|-------|
| 1 | `http://huangaybantiep.xyz` | URL | Phishing URL in email body | #1 |
| 2 | `146.56.195.192` | IP | Sender SMTP IP — flagged malicious | #1 |
| 3 | `lethuyan852@gmail.com` | E-mail Sender | Random-numbered Gmail sender | #1 |
| 4 | `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0` | Hash | Malicious macro file (Logan family) | #2 |
| 5 | `92.204.221.16` | IP | C2 IP — host of dropped payload | #2 |
| 6 | `heg.com` | Domain | C2 domain | #2 |
| 7 | `greyhathacker.net` | Domain | Dropper domain (messbox.exe) | #2 |
| 8 | `134.209.145.73` | IP | Malicious source IP — DigitalOcean, India | #3 |
| 9 | `185.107.80.128` | IP | Attacker — 79 abuse reports, NForce VPN | #4 |
| 10 | `analyst` | Account | Compromised user account (Linux) | #4 |
| 11 | `test` | Account | Compromised user account (Linux) | #4 |
| 12 | `172.16.17.186` | IP | Target host — VirtuLinux | #4 |
| 13 | `181.214.131.108` | IP | Attacker — 33 abuse reports, RDP brute force | #5 |
| 14 | `5D75D0EA8BBBB5B652F7B72CF728C00322BD486D54A5C49...` | Hash | WINWORD.EXE launcher hash | #5 |
| 15 | `Normal.dotm` | File | Office template modified for persistence | #5 |

---

## 💡 Key Lessons Learned

1. **VirusTotal "0 detections" ≠ safe** — freshness of domains matters more than detection count (Alert #1)
2. **Pyramid of evidence** — no single indicator is conclusive; the combined correlation is (Alert #1)
3. **Log Management affects severity, not verdict** — no click lowers severity but email is still malicious (Alert #1)
4. **IOC type classification matters** — URL Address vs IP Address vs E-mail Sender (Alert #1)
5. **Playbooks mirror enterprise SOC tools** — Splunk SOAR, Cortex XSOAR, Microsoft Sentinel (Alert #1)
6. **⚠️ Log search is multi-angle** — process names (`powershell.exe`) matter more than IOCs (Alert #2)
7. **"Device Action: Blocked" tells you severity** — blocked = contained, lower severity (Alert #3)
8. **Cloud IPs need context** — a DigitalOcean IP isn't automatically bad, but with 5+ detections + bruteforce history → confirmed malicious (Alert #3)
9. **⚠️ Pro view > Basic view** — Basic returned 0 events; Pro view found the confirmed compromise (Alert #4)
10. **AbuseIPDB complements VirusTotal** — VT showed 0/91, AbuseIPDB showed 79 reports (Alert #4)
11. **"Accepted password" in SSH logs = compromise confirmed** — the smoking gun (Alert #4)
12. **Scope = THIS alert's indicators only** — broader patterns go in notes, not scope answers (Alert #4)
13. **⭐ Timing correlation proves causation** — successful RDP login 4 minutes before template modification (Alert #5)
14. **`Normal.dotm` = global Word template = full persistence** — every document runs the macro (Alert #5)
15. **RDP brute force (port 3389) is common in enterprise** — always check for successful EventID 4624 (Alert #5)
16. **100% playbook score is achievable** with methodical investigation (Alert #5)

---

## 📸 Screenshots

All investigation evidence is stored in [`./screenshots/`](./screenshots/) — named by alert ID and stage:

| Alert | Screenshots |
|-------|-------------|
| #1 | 11 screenshots — phishing triage workflow |
| #2 | 13 screenshots — malware triage workflow |
| #3 | 5 screenshots — cloud access attempt workflow |
| #4 | 8 screenshots — confirmed compromise investigation |
| #5 | 9 screenshots — RDP brute force + template persistence |

---

## 📁 Related Files

- [`triage-log.md`](./triage-log.md) — Detailed writeups for every alert
- [`screenshots/`](./screenshots/) — Investigation evidence
- [Root README](../../README.md) — Full portfolio overview

---

## 🎓 Skills Demonstrated in This Project

- Alert triage workflow (L1 SOC)
- Email phishing investigation
- Malware analysis (macro dropper, Logan family)
- Cloud region access investigation
- **Linux SSH brute force investigation**
- **Windows RDP brute force investigation**
- **Confirmed compromise identification**
- **Office template persistence investigation**
- **Multi-tool threat intelligence correlation (VirusTotal + AbuseIPDB)**
- **Log forensics via Pro view / raw log search**
- **Incident response playbook execution (100% score achieved)**
- **Host containment action**
- Threat intelligence lookups
- Log correlation and analysis
- IOC extraction and classification
- MITRE ATT&CK mapping (19 techniques)
- Evidence documentation with screenshots
- Analytical writing (analyst notes, verdicts)

---

*Last updated: October 7, 2026*
