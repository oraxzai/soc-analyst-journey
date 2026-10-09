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
| ID Ransomware / Crypto Sheriff | Ransomware family identification |
| MITRE ATT&CK | Threat technique mapping |
| Log Management (LetsDefend) | Internal log correlation (Basic + Pro views) |

---

## 📋 Alert Progress

| # | Alert | Severity | Verdict | Date | Log |
|---|-------|----------|---------|------|-----|
| 1 | SOC101 - Phishing Mail Detected (EventID 87) | Medium | ✅ True Positive | 2026-10-02 | [log](./triage-log.md#alert-1--soc101-phishing-mail-detected) |
| 2 | SOC205 - Malicious Macro Executed (EventID 231) | High | ✅ True Positive | 2026-10-03 | [log](./triage-log.md#alert-2--soc205-malicious-macro-executed) |
| 3 | SOC325 - Unauthorized Cloud Region Access (EventID 303) | Low | ✅ True Positive | 2026-10-04 | [log](./triage-log.md#alert-3--soc325-unauthorized-cloud-region-access) |
| 4 | SOC276 - Account Discovery Attempt (EventID 251) | **Critical** | ✅ True Positive | 2026-10-06 | [log](./triage-log.md#alert-4--soc276-account-discovery-attempt) |
| 5 | SOC312 - Unauthorized Template Modification (EventID 290) | **Critical** | ✅ True Positive | 2026-10-07 | [log](./triage-log.md#alert-5--soc312-unauthorized-template-modification) |
| 6 | SOC293 - Exfiltration Over Pastebin (EventID 269) | **Critical** | ✅ True Positive | 2026-10-07 | [log](./triage-log.md#alert-6--soc293-exfiltration-over-pastebin) |
| 7 | SOC317 - Possible VM Detection Attempt (EventID 295) | **Critical** | ✅ True Positive | 2026-10-09 | [log](./triage-log.md#alert-7--soc317-possible-vm-detection-attempt) |
| 8 | SOC328 - Akira Ransomware IOC's Detected (EventID 306) | High | ✅ True Positive | 2026-10-09 | [log](./triage-log.md#alert-8--soc328-akira-ransomware-iocs-detected) |
| 9 | *(pending)* | | | | |
| 10 | *(pending)* | | | | |

**Progress:** 8 / 10 alerts triaged (80%)

---

## 📊 Stats

| Metric | Value |
|--------|-------|
| Total Alerts Triaged | 8 |
| True Positives | 8 |
| False Positives | 0 |
| Benign | 0 |
| Critical Severity Confirmed | 4 |
| Confirmed Compromises | 5 |
| Confirmed Data Exfiltration | 1 |
| Confirmed Ransomware | 1 |
| Average Time per Alert | ~2 hours (learning pace) |
| MITRE Techniques Mapped | 35+ |
| IOCs Extracted | 30+ |
| Perfect Playbook Scores | 2 |

---

## 🧭 MITRE ATT&CK Techniques Covered

| Tactic | Technique | ID | Alert |
|--------|-----------|-----|-------|
| Initial Access | Phishing: Spearphishing Link | T1566.002 | #1 |
| Initial Access | Phishing: Spearphishing Attachment | T1566.001 | #2, #6, #8 |
| Execution | PowerShell | T1059.001 | #2, #6, #7, #8 |
| Execution | Windows Command Shell | T1059.003 | #2, #8 |
| Execution | User Execution: Malicious File | T1204.002 | #2, #6 |
| Execution | Visual Basic | T1059.005 | #5 |
| Execution | Unix Shell | T1059.004 | #4 |
| Command & Control | Application Layer Protocol | T1071 | #2 |
| Command & Control | Ingress Tool Transfer | T1105 | #2 |
| Command & Control | Non-Standard Port | T1571 | #2 |
| Command & Control | Web Protocols | T1071.001 | #5 |
| Resource Development | Compromise Accounts | T1586 | #3 |
| Defense Evasion | Valid Accounts | T1078 | #3, #4, #5, #7 |
| Defense Evasion | Unused Cloud Regions | T1535 | #3 |
| Defense Evasion | Template Injection | T1221 | #5 |
| Defense Evasion | Virtualization/Sandbox Evasion | T1497.001 | #7 |
| Initial Access | External Remote Services (RDP) | T1133 | #3, #4, #5, #7 |
| Credential Access | Brute Force | T1110 | #4, #5, #7 |
| Discovery | Account Discovery | T1087 | #4, #7 |
| Discovery | System Owner/User Discovery | T1033 | #6 |
| Discovery | System Information Discovery | T1082 | #7 |
| Discovery | Process Discovery | T1057 | #7 |
| Persistence | Office Template Macros | T1137.001 | #5 |
| Persistence | Shortcut Modification | T1547.009 | #6 |
| Exfiltration | Exfiltration Over Web Service | T1567.002 | #6 |
| Impact | Data Destruction | T1485 | #8 |
| Impact | Data Encrypted for Impact | T1486 | #8 |
| Impact | Inhibit System Recovery | T1490 | #8 |
| Execution | Windows Management Instrumentation | T1047 | #7, #8 |

---

## 🎯 IOCs Extracted

| # | IOC | Type | Context | Alert |
|---|-----|------|---------|-------|
| 1 | `http://huangaybantiep.xyz` | URL | Phishing URL in email body | #1 |
| 2 | `146.56.195.192` | IP | Sender SMTP IP — flagged malicious | #1 |
| 3 | `lethuyan852@gmail.com` | E-mail Sender | Random-numbered Gmail sender | #1 |
| 4 | `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0` | Hash | Malicious macro (Logan family) | #2 |
| 5 | `92.204.221.16` | IP | C2 IP | #2 |
| 6 | `heg.com` | Domain | C2 domain | #2 |
| 7 | `greyhathacker.net` | Domain | Dropper domain | #2 |
| 8 | `134.209.145.73` | IP | Malicious source (DigitalOcean, India) | #3 |
| 9 | `185.107.80.128` | IP | Attacker — 79 abuse reports | #4 |
| 10 | `analyst`, `test` | Accounts | Compromised Linux users | #4 |
| 11 | `172.16.17.186` | IP | Victim host — VirtuLinux | #4 |
| 12 | `181.214.131.108` | IP | Attacker — 33 abuse reports, RDP | #5 |
| 13 | `5D75D0EA8BBBB5B652F7B72CF728C00322BD486D54A5C49...` | Hash | WINWORD.EXE launcher | #5 |
| 14 | `Normal.dotm` | File | Office template persistence | #5 |
| 15 | `info@dachfix.com` | E-mail Sender | Phishing sender | #6 |
| 16 | `103.145.252.87` | IP | Sender SMTP — 8/92 malicious, Vietnam | #6 |
| 17 | `dachfix.com` | Domain | Phishing domain | #6 |
| 18 | `https://files-ld.s3.us-east-2.amazonaws.com/quick-zip.fix` | URL | Malware download | #6 |
| 19 | `2f2d8121d6b351a32a5c55995450200f3cafd3d26b2cf5f646cd3a80f175450e` | SHA256 | Malicious ZIP (lnkscript) | #6 |
| 20 | `system_users.ps1` | File | Malicious script | #6 |
| 21 | `pastebin.com` | Domain | Exfiltration destination | #6 |
| 22 | `104.20.3.235` | IP | Pastebin/Cloudflare endpoint | #6 |
| 23 | `37.19.205.203` | IP | Attacker — 156 abuse reports, Japan | #7 |
| 24 | `172.16.17.118` | IP | Victim host — Anemon | #7 |
| 25 | `Get-WmiObject Win32_ComputerSystem` | Command | VM detection | #7 |
| 26 | `2C7AEAC07CE7F03B74952E0E243BD52F2BFA60FADC92DD71A6A1FEE2D14CDD77` | Hash | Akira ransomware EXE | #8 |
| 27 | `172.16.17.130` | IP | Victim host — Vergil | #8 |
| 28 | `akira_readme.txt` | File | Akira ransom note | #8 |
| 29 | `payment-confirmation-invoice-12345.zip` | File | Phishing attachment | #8 |
| 30 | `149.154.167.99`, `3.226.70.65` | IPs | Suspected C2/exfil destinations | #8 |

---

## 💡 Key Lessons Learned

1. **VirusTotal "0 detections" ≠ safe** — freshness matters more than detection count (Alert #1)
2. **Pyramid of evidence** — combined correlation beats single indicators (Alert #1)
3. **⚠️ Log search is multi-angle** — process names matter more than IOCs (Alert #2)
4. **"Device Action: Blocked" lowers severity** (Alert #3)
5. **⚠️ Pro view > Basic view** — Pro found the confirmed compromise (Alert #4)
6. **AbuseIPDB complements VirusTotal** — VT 0/91, AbuseIPDB 79 reports (Alert #4)
7. **"Accepted password" in SSH logs = compromise confirmed** (Alert #4)
8. **Scope = THIS alert's indicators only** (Alert #4)
9. **⭐ Timing correlation proves causation** (Alert #5, #7)
10. **`Normal.dotm` = full persistence** (Alert #5)
11. **RDP brute force (port 3389) is common in enterprise** (Alert #5, #7)
12. **100% playbook score is achievable** (Alert #5, #7)
13. **⚠️ Phishing → download → execution → exfil is a full kill chain** (Alert #6)
14. **LNKScript family uses shortcut files for execution** (Alert #6)
15. **Pastebin is a common exfiltration destination** (Alert #6)
16. **Chrome downloads can still be malicious** (Alert #6)
17. **VM detection via WMI = sandbox evasion** (Alert #7)
18. **⚠️ Ransom notes go to ID Ransomware** — even without encrypted files (Alert #8)
19. **⚠️ File extension searches need broader terms** (Alert #8)
20. **Ransomware runs in seconds** — 62-second attack window (Alert #8)
21. **"Detected" ≠ "Blocked"** — verify with encryption events (Alert #8)

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
| #6 | 12 screenshots — full exfiltration kill-chain investigation |
| #7 | 5 screenshots — VM detection + RDP compromise |
| #8 | 6 screenshots — Akira ransomware investigation |

**Total:** 69 screenshots across 8 alerts

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
- **Data exfiltration investigation (Pastebin)**
- **Full kill-chain reconstruction**
- **VM/sandbox evasion detection**
- **Ransomware investigation (Akira)**
- **Multi-tool threat intelligence correlation**
- **Log forensics via Pro view / raw log search**
- **Incident response playbook execution (100% scores achieved)**
- **Host containment action (4 hosts contained)**
- IOC extraction and classification (30 IOCs)
- MITRE ATT&CK mapping (29 techniques)
- Evidence documentation with screenshots
- Analytical writing (analyst notes, verdicts)

---

*Last updated: October 9, 2026*
