# Triage Log — Project 01

Detailed writeups of each alert triaged on LetsDefend.

**Progress:** 8 / 10 alerts triaged  
**Last Updated:** October 9, 2026

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
- Code Insights: Macro triggers on form focus, runs hidden shell command
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
- VT IP: 5/91 malicious (BitDefender, CRDF, Criminal IP, G-Data, SOCRadar)
- ASN: DigitalOcean, India
- CrowdSec: "SSH Bruteforce" history
- Log Mgmt: 0 events for source IP/user

### 🎯 Verdict
**TRUE POSITIVE — Unauthorized Access (Blocked)** | Severity: Low

### 💡 Lessons
1. "Device Action: Blocked" → lower severity
2. Cloud IPs need context

---

## Alert #4 — SOC276: Account Discovery Attempt

**EventID:** 251 | **Severity:** CRITICAL | **Date:** 2026-10-06

### 📋 Overview
- Host: VirtuLinux (Ubuntu 20.04, 172.16.17.186)
- Command: `getent passwd`
- L1 Note: Brute force from 185.107.80.128, success unknown

### 🔍 Investigation
- AbuseIPDB: 185.107.80.128 — **79 reports**, 25% confidence
- VT: 0/91 (NForce VPN, Netherlands)
- Log Mgmt (Pro view): `accepted AND 185.107.80.128` → **3 successful logins**
  - 08:41:56 → analyst, test accounts
  - 08:42:28 → analyst
- Broader `accepted` search: 37 events (env-wide pattern)

### 🎯 Verdict
**TRUE POSITIVE — CONFIRMED COMPROMISE** | Severity: Critical
**Playbook Score:** 25 (92%)

### ⚠️ Correction
**Wrong:** Scope = Yes (multiple devices) | **Correct:** No — only THIS alert's IP

### 🛡️ Actions
- Contained VirtuLinux
- Escalated to IR

---

## Alert #5 — SOC312: Unauthorized Template Modification

**EventID:** 290 | **Severity:** CRITICAL | **Date:** 2026-10-07

### 📋 Overview
- Host: Jonah (172.16.17.110)
- Process: WINWORD.EXE
- Command: `WINWORD.EXE /n /f "...\Normal.dotm"`

### 🔍 Investigation
- AbuseIPDB: 181.214.131.108 — 33 reports, 11% confidence
- Log Mgmt: 10 events
  - 9 failed RDP logins → **1 SUCCESS at 15:27:20**
  - WINWORD modified `Normal.dotm` at 15:31:02 (**4 min later**)
- Normal.dotm = global Word template = **persistence**

### 🎯 Verdict
**TRUE POSITIVE — CONFIRMED COMPROMISE + PERSISTENCE** | Severity: Critical
**Playbook Score:** 30 (**100%**) ⭐

### 🛡️ Actions
- Confirmed compromise via timing correlation
- Identified Normal.dotm persistence
- Contained host Jonah

### 💡 Lessons
1. **Timing correlation proves causation**
2. **`Normal.dotm` = full persistence**
3. **RDP brute force is common** — always check EventID 4624

---

## Alert #6 — SOC293: Exfiltration Over Pastebin

**EventID:** 269 | **Severity:** CRITICAL | **Date:** 2026-10-07

### 📋 Overview
- Host: Gabriela (172.16.17.63)
- File: `system_users.ps1`
- Command: `powershell.exe -ExecutionPolicy Bypass -File .\system_users.ps1`
- Device Action: **Allowed** ⚠️

### 🔍 Investigation
**Phishing origin:**
- From: `info@dachfix.com` | IP: 103.145.252.87 (Vietnam — 8/92 malicious)
- Subject: "Download and Apply the Critical Fix"
- Attachment: Quick-Fix.zip

**Download from cloud:**
- Chrome → `https://files-ld.s3.us-east-2.amazonaws.com/quick-zip.fix`
- Hash: `2f2d8121d6b351a32a5c55995450200f3cafd3d26b2cf5f646cd3a80f175450e` — `lnkscript` family

**PowerShell + Exfil:**
- DNS query: `pastebin.com` → 104.20.3.235
- HTTPS upload to port 443 — **ALLOWED → DATA LEAKED**

### 🎯 Verdict
**TRUE POSITIVE — CONFIRMED DATA EXFILTRATION** | Severity: Critical

### 🧭 MITRE
- T1566.001, T1204.002, T1059.001, T1547.009, T1033, T1567.002

### 💡 Lessons
1. Full kill chain: phishing → download → execution → exfil
2. "Urgent fix" emails = classic phishing lures
3. Pastebin = common exfil destination
4. Chrome downloads can still be malicious

---

## Alert #7 — SOC317: Possible VM Detection Attempt

**EventID:** 295 | **Severity:** CRITICAL | **Date:** 2026-10-09

### 📋 Overview
- Host: Anemon (172.16.17.118)
- Command: `Get-WmiObject -Class Win32_ComputerSystem`
- L1 Note: Brute force from 37.19.205.203, success unknown

### 🔍 Investigation
- AbuseIPDB: 37.19.205.203 — **156 reports**, 35% confidence, Datacamp VPN, Japan
- Log Mgmt: 14 events
  - 13 failed RDP logins → **1 SUCCESS at 14:30:31**
  - `Get-WmiObject` ran at 14:33:33 (**3 min later**)
- `Get-WmiObject Win32_ComputerSystem` = **VM/sandbox detection**

### 🎯 Verdict
**TRUE POSITIVE — CONFIRMED COMPROMISE + VM DETECTION** | Severity: Critical
**Playbook Score:** 30 (**100%**) ⭐

### 🛡️ Actions
- Confirmed successful RDP login
- Identified VM detection post-compromise
- Contained host Anemon

### 💡 Lessons
1. **T1497.001 — Virtualization/Sandbox Evasion**
2. Timing correlation (3 min gap) confirms attack chain
3. Same pattern as Alerts #4 and #5

---

## Alert #8 — SOC328: Akira Ransomware IOC's Detected

**EventID:** 306 | **Severity:** HIGH | **Date:** 2026-10-09

### 📋 Overview
- Host: Vergil (172.16.17.130)
- File: `payment-confirmation-invoice-12345\Payment Confirmation Invoice #12345.exe`
- Hash: `2C7AEAC07CE7F03B74952E0E243BD52F2BFA60FADC92DD71A6A1FEE2D14CDD77`
- Difficulty: **Hard**
- Type: APT Group

### 🔍 Investigation

**Threat Intelligence:**
- VT hash: **60/71 malicious** — `ransomware.akira/misc`
- Family: akira, misc, encoder
- Anti-analysis tags: `checks-user-input`, `long-sleeps`, `detect-debug-environment`

**Attack Timeline:**
- 07:52:10 → EventID 4688 — Malicious EXE executed
- 07:53:12-13 → Ransom note `akira_readme.txt` dropped at `C:\Users\Public\Downloads\`
- Total window: **~62 seconds**

**Containment Evidence:**
- `vssadmin` search: **0 events** (no shadow deletion)
- `.akira` extension search: **0 events** (no encryption logged)
- 22 outbound HTTPS connections (possible C2/exfil)

**Initial Access:**
- Phishing email with `payment-confirmation-invoice-12345.zip`
- User Vergil executed the extracted EXE

### 🎯 Verdict
**TRUE POSITIVE — Akira Ransomware (Execution Confirmed, Encryption Prevented)**  
**Severity:** High  
**Confidence:** High

**Playbook Score:** ~75% (2 wrong answers)

### ⚠️ Playbook Corrections
| Question | My Answer | Correct |
|----------|-----------|---------|
| Automated Categorization Services | No | **Yes** (upload ransom note to ID Ransomware) |
| Determine type - 3 | No | **Yes** (encrypted file extensions may exist) |

### 🛡️ Actions Taken
- Confirmed Akira via VirusTotal
- Documented full attack timeline (62 sec)
- Verified encryption was prevented
- Escalated to IR (Create Ticket)

### 📊 IOCs
- Hash: `2C7AEAC07CE7F03B74952E0E243BD52F2BFA60FADC92DD71A6A1FEE2D14CDD77`
- Host: Vergil (172.16.17.130)
- Ransom note: `akira_readme.txt`
- Phishing attachment: `payment-confirmation-invoice-12345.zip`

### 🧭 MITRE
- T1566.001 — Phishing: Spearphishing Attachment
- T1047 — Windows Management Instrumentation
- T1059.001 — PowerShell
- T1059.003 — Windows Command Shell
- T1485 — Data Destruction
- T1486 — Data Encrypted for Impact
- T1490 — Inhibit System Recovery

### 💡 Lessons Learned
1. **Ransom notes go to ID Ransomware** — even without encrypted files
2. **File extension searches need broader terms** — `.akira` may not match log format
3. **Ransomware runs in seconds** — 62-second attack window
4. **"Detected" ≠ "Blocked"** — verify with encryption events
5. **Playbook scores teach reasoning** — read the explanations

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

**Total:** 8 alerts | **8 True Positives** | **0 False Positives**  
**Progress:** 8 / 10 (80%)

---

*Last updated: October 9, 2026*
