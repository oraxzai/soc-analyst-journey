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
| AbuseIPDB | IP address reputation |
| urlscan.io | URL/domain analysis |
| MITRE ATT&CK | Threat technique mapping |
| Log Management (LetsDefend) | Internal log correlation |

---

## 📋 Alert Progress

| # | Alert | Severity | Verdict | Date | Log |
|---|-------|----------|---------|------|-----|
| 1 | SOC101 - Phishing Mail Detected (EventID 87) | Medium | ✅ True Positive | 2026-10-02 | [log](./triage-log.md#alert-1--soc101-phishing-mail-detected) |
| 2 | SOC205 - Malicious Macro Executed (EventID 231) | High | ✅ True Positive | 2026-10-03 | [log](./triage-log.md#alert-2--soc205-malicious-macro-has-been-executed) |
| 3 | SOC325 - Unauthorized Cloud Region Access (EventID 303) | Low | ✅ True Positive | 2026-10-04 | [log](./triage-log.md#alert-3--soc325-unauthorized-cloud-region-access-attempt) |
| 4 | *(pending)* | | | | |
| 5 | *(pending)* | | | | |
| 6 | *(pending)* | | | | |
| 7 | *(pending)* | | | | |
| 8 | *(pending)* | | | | |
| 9 | *(pending)* | | | | |
| 10 | *(pending)* | | | | |

**Progress:** 3 / 10 alerts triaged (30%)

---

## 📊 Stats

| Metric | Value |
|--------|-------|
| Total Alerts Triaged | 3 |
| True Positives | 3 |
| False Positives | 0 |
| Benign | 0 |
| Average Time per Alert | ~1.8 hours (learning pace) |
| MITRE Techniques Mapped | 12 |
| IOCs Extracted | 8 |

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
| Defense Evasion | Valid Accounts | T1078 | #3 |
| Initial Access | External Remote Services | T1133 | #3 |
| Defense Evasion | Unused/Unsupported Cloud Regions | T1535 | #3 |

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

---

## 📸 Screenshots

All investigation evidence is stored in [`./screenshots/`](./screenshots/) — named by alert ID and stage:

| Alert | Screenshots |
|-------|-------------|
| #1 | 11 screenshots — phishing triage workflow |
| #2 | 13 screenshots — malware triage workflow |
| #3 | 5 screenshots — cloud access attempt workflow |

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
- Threat intelligence lookups (VirusTotal, urlscan.io)
- Log correlation and analysis
- IOC extraction and classification
- Incident response playbook execution
- MITRE ATT&CK mapping
- Evidence documentation with screenshots
- Analytical writing (analyst notes, verdicts)

---

*Last updated: October 4, 2026*
