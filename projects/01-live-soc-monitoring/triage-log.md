# Triage Log — Project 01

Detailed writeups of each alert triaged on LetsDefend.

**Progress:** 9 / 10 alerts triaged  
**Last Updated:** October 10, 2026

---

## Alert #1 — SOC101: Phishing Mail Detected

**EventID:** 87 | **Severity:** Medium | **Date:** 2026-10-02

### 📋 Overview
- Rule: SOC101 - Phishing Mail Detected
- Sender: lethuyan852@gmail.com
- Recipient: mark@letsdefend.io
- URL: http://huangaybantiep.xyz
- Device Action: Allowed

### 🔍 Investigation
- VT URL: 2/92 Suspicious (fresh .xyz domain)
- VT IP: 146.56.195.192 — Criminal IP flags Malicious, AS45090 China
- Log Mgmt: 0 events for URL, recipient, sender IP

### 🎯 Verdict
**TRUE POSITIVE — Malicious Phishing Email** | Severity: Medium

### 🧭 MITRE
- T1566.002 — Phishing: Spearphishing Link

### 💡 Lessons
1. VT "0 detections" ≠ safe
2. Pyramid of evidence
3. Log Mgmt affects severity, not verdict

---

## Alert #2 — SOC205: Malicious Macro Executed

**EventID:** 231 | **Severity:** High | **Date:** 2026-10-03

### 📋 Overview
- Host: Jayne (172.16.171.98)
- Hash: `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0`
- File: edit1-invoice.docx

### 🔍 Investigation
- VT hash: **31/65 malicious** — downloader.logan/w97m
- Endpoint: Containment OFF
- Log Mgmt: 6 searches, all 0 events initially

### 🎯 Verdict
**TRUE POSITIVE — Malicious Macro (Logan)** | Severity: High
**Playbook Score:** 10 (75%)

### ⚠️ Correction
**Wrong:** C2 not accessed | **Correct:** C2 accessed via PowerShell to greyhathacker.net

### 💡 Lessons
**Search process names FIRST, then IOCs**
- Tier 1: `powershell.exe`, `cmd.exe`, `wscript.exe`, `mshta.exe`, `rundll32.exe`
- Tier 2: File names
- Tier 3: IOCs

---

## Alert #3 — SOC325: Unauthorized Cloud Region Access

**EventID:** 303 | **Severity:** Low | **Date:** 2026-10-04

### 📋 Overview
- User: test@letsdefend.io
- Source IP: 134.209.145.73
- Device Action: **Blocked**

### 🔍 Investigation
- VT IP: 5/91 malicious (DigitalOcean, India)
- CrowdSec: "SSH Bruteforce" history
- Log Mgmt: 0 events

### 🎯 Verdict
**TRUE POSITIVE — Unauthorized Access (Blocked)** | Severity: Low

### 💡 Lessons
1. "Device Action: Blocked" → lower severity
2. Cloud IPs need context

---

## Alert #4 — SOC276: Account Discovery Attempt

**EventID:** 251 | **Severity:** CRITICAL | **Date:** 2026-10-06

### 📋 Overview
- Host: VirtuLinux (172.16.17.186)
- Command: `getent passwd`
- L1 Note: Brute force from 185.107.80.128, success unknown

### 🔍 Investigation
- AbuseIPDB: 185.107.80.128 — **79 reports**
- Log Mgmt (Pro view): `accepted AND 185.107.80.128` → **3 successful logins**
  - 08:41:56 → test
  - 08:41:59 → analyst
  - 08:42:28 → analyst

### 🎯 Verdict
**TRUE POSITIVE — CONFIRMED COMPROMISE** | Severity: Critical
**Playbook Score:** 25 (92%)

### ⚠️ Correction
**Wrong:** Scope = Yes | **Correct:** No — only THIS alert's IP

### 🛡️ Actions
- Contained VirtuLinux
- Escalated to IR

---

## Alert #5 — SOC312: Unauthorized Template Modification

**EventID:** 290 | **Severity:** CRITICAL | **Date:** 2026-10-07

### 📋 Overview
- Host: Jonah (172.16.17.110)
- Command: `WINWORD.EXE /n /f "...\Normal.dotm"`

### 🔍 Investigation
- AbuseIPDB: 181.214.131.108 — 33 reports
- Log Mgmt: 9 failed RDP → **1 SUCCESS at 15:27:20**
- WINWORD modified `Normal.dotm` at 15:31:02 (**4 min later**)

### 🎯 Verdict
**TRUE POSITIVE — CONFIRMED COMPROMISE + PERSISTENCE** | Severity: Critical
**Playbook Score:** 30 (**100%**) ⭐

### 🛡️ Actions
- Contained host Jonah

### 💡 Lessons
1. **Timing correlation proves causation**
2. **`Normal.dotm` = full persistence**
3. **RDP brute force is common**

---

## Alert #6 — SOC293: Exfiltration Over Pastebin

**EventID:** 269 | **Severity:** CRITICAL | **Date:** 2026-10-07

### 📋 Overview
- Host: Gabriela (172.16.17.63)
- File: `system_users.ps1`
- Command: `powershell.exe -ExecutionPolicy Bypass -File .\system_users.ps1`

### 🔍 Investigation
**Phishing origin:**
- From: `info@dachfix.com` (Vietnam — 8/92 malicious)
- Attachment: Quick-Fix.zip

**Malware download:**
- Chrome → `https://files-ld.s3.us-east-2.amazonaws.com/quick-zip.fix`
- Hash: `2f2d8121...` — lnkscript family

**Exfiltration:**
- DNS query: `pastebin.com` → 104.20.3.235
- HTTPS upload — **ALLOWED → DATA LEAKED**

### 🎯 Verdict
**TRUE POSITIVE — CONFIRMED DATA EXFILTRATION** | Severity: Critical

### 🧭 MITRE
- T1566.001, T1204.002, T1059.001, T1547.009, T1033, T1567.002

### 💡 Lessons
1. Full kill chain confirmed
2. "Urgent fix" emails = phishing
3. Pastebin = common exfil destination

---

## Alert #7 — SOC317: Possible VM Detection Attempt

**EventID:** 295 | **Severity:** CRITICAL | **Date:** 2026-10-09

### 📋 Overview
- Host: Anemon (172.16.17.118)
- Command: `Get-WmiObject -Class Win32_ComputerSystem`
- L1 Note: Brute force from 37.19.205.203

### 🔍 Investigation
- AbuseIPDB: 37.19.205.203 — **156 reports**, Datacamp VPN, Japan
- Log Mgmt: 13 failed RDP → **1 SUCCESS at 14:30:31**
- `Get-WmiObject` ran at 14:33:33 (**3 min later**)
- VM detection = T1497.001 (sandbox evasion)

### 🎯 Verdict
**TRUE POSITIVE — CONFIRMED COMPROMISE + VM DETECTION** | Severity: Critical
**Playbook Score:** 30 (**100%**) ⭐

### 🛡️ Actions
- Contained host Anemon

### 💡 Lessons
1. T1497.001 — Virtualization/Sandbox Evasion
2. Timing correlation confirms attack chain

---

## Alert #8 — SOC328: Akira Ransomware IOC's Detected

**EventID:** 306 | **Severity:** HIGH | **Date:** 2026-10-09

### 📋 Overview
- Host: Vergil (172.16.17.130)
- File: `payment-confirmation-invoice-12345\Payment Confirmation Invoice #12345.exe`
- Hash: `2C7AEAC07CE7F03B74952E0E243BD52F2BFA60FADC92DD71A6A1FEE2D14CDD77`
- Difficulty: **Hard**

### 🔍 Investigation
- VT hash: **60/71 malicious** — ransomware.akira/misc
- Anti-analysis tags: `checks-user-input`, `long-sleeps`, `detect-debug-environment`
- Process created: 07:52:10 (EventID 4688)
- Ransom note `akira_readme.txt` dropped: 07:53:12-13
- Attack window: ~62 seconds
- `vssadmin` search: 0 events (no shadow deletion)
- `.akira` extension search: 0 events (no encryption logged)
- 22 outbound HTTPS connections (possible C2/exfil)

### 🎯 Verdict
**TRUE POSITIVE — Akira Ransomware (Execution Confirmed, Encryption Prevented)**  
**Severity:** High

### ⚠️ Playbook Corrections (2 wrong)
| Question | My Answer | Correct |
|----------|-----------|---------|
| Automated Categorization Services | No | **Yes** |
| Determine type - 3 | No | **Yes** |

### 💡 Lessons
1. Ransom notes go to ID Ransomware
2. File extension searches need broader terms
3. Ransomware runs in seconds
4. "Detected" ≠ "Blocked"

---

## Alert #9 — SOC194: Possible Reverse Shell Detected

**EventID:** 144 | **Severity:** HIGH | **Date:** 2026-10-10

### 📋 Overview
- Host: Can (172.16.17.20)
- File: `C:\Users\LetsDefend\Downloads\2022_Annual_Report.docx`
- EDR Action: Detected
- Difficulty: **Hard**

### 🔍 Investigation

**Initial Access — Phishing Email:**
- From: `mate@instagram.com.tr`
- To: `can@letsdefend.io`
- Subject: "Your Instagram Account Has Been Compromised"
- Sender IP: 172.16.20.3 (internal — investigate)
- Time: 2023-05-03 17:55:38

**Phishing Site Visit:**
- Domain: `lnstagrams.com.tr` (typosquatting — lowercase L)
- Host IP: 31.210.39.247 (VT: 1/92 Phishing, Turkey)
- Time: 2023-05-04 09:45:17

**Execution + Persistence:**
- Command: `WINWORD.EXE /n /f "...\Custom Office Templates\test.dotm"`
- Time: 2023-05-04 07:55:24
- Technique: **Office Template Macros (T1137.001)**

**Reverse Shell Attempt:**
- Destination: 185.106.94.194:3389 (RDP port)
- Attempts: 2 outbound connections
- **Result: BOTH FAILED** (firewall blocked)

**Containment:**
- Files deleted: 2022_Annual_Report.docx, test.dotm
- Host isolated

### 🎯 Verdict
**TRUE POSITIVE — Reverse Shell Attempt (Blocked)**  
**Severity:** High  
**Playbook Score:** 10 (75%)

### ⚠️ Playbook Correction
**Wrong:** "Was the backdoor exploited?" — said No | **Correct:** Yes (mechanism triggered)

### 📊 IOCs
- `mate@instagram.com.tr` (Phishing Sender)
- `instagram.com.tr` (Phishing Domain)
- `lnstagrams.com.tr` (Typosquatting Domain)
- `31.210.39.247` (Phishing Host IP)
- `185.106.94.194` (Reverse Shell Destination)
- `172.16.20.3` (Internal Sender — investigate)
- `2022_Annual_Report.docx` (Malicious Document)
- `test.dotm` (Malicious Template)

### 🧭 MITRE
- T1566.002 — Phishing: Spearphishing Link
- T1204.002 — User Execution: Malicious File
- T1137.001 — Office Template Macros
- T1071 — Application Layer Protocol
- T1571 — Non-Standard Port

### 💡 Lessons
1. Typosquatting: `lnstagrams` (lowercase L) vs `instagram`
2. Office template persistence via `test.dotm`
3. Reverse shell used RDP port (3389) to blend in
4. Check internal sender IPs — could be compromised relay
5. **"Backdoor exploited" ≠ "connection succeeded"** — mechanism triggered counts

---

## 📊 Project Summary

| Alert | EventID | Type | Verdict | Severity |
|:-----:|:-------:|------|:-------:|:--------:|
| #1 | 87 | Phishing | ✅ True Positive | Medium |
| #2 | 231 | Malware | ✅ True Positive | High |
| #3 | 303 | Cloud Access | ✅ True Positive | Low |
| #4 | 251 | Account Discovery | ✅ True Positive | **Critical** |
| #5 | 290 | Template Mod | ✅ True Positive | **Critical** |
| #6 | 269 | Exfiltration | ✅ True Positive | **Critical** |
| #7 | 295 | VM Detection | ✅ True Positive | **Critical** |
| #8 | 306 | Ransomware | ✅ True Positive | High |
| #9 | 144 | Reverse Shell | ✅ True Positive | High |

**Total:** 9 alerts | **9 True Positives** | **0 False Positives**  
**Progress:** 9 / 10 (90%)

---

*Last updated: October 10, 2026*
