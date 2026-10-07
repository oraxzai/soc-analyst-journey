# Triage Log — Project 01

Detailed writeups of each alert triaged on LetsDefend.

**Progress:** 5 / 10 alerts triaged  
**Last Updated:** October 7, 2026

---

## Alert #1 — SOC101: Phishing Mail Detected

**EventID:** 87  
**Difficulty:** Beginner  
**Severity:** Medium  
**Date Triaged:** 2026-10-02  
**Analyst:** Muhammad Haris (L1 SOC Trainee)

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC101 - Phishing Mail Detected |
| Event Time | 2021-04-04 23:00:15 +03:00 |
| SMTP Address | 146.56.195.192 |
| Device Action | Allowed (email delivered) |
| Sender | lethuyan852@gmail.com |
| Recipient | mark@letsdefend.io |
| Embedded URL | http://huangaybantiep.xyz |

### 🔍 Investigation

**Email content:** "Check out this product! Your life will be less difficult. http://huangaybantiep.xyz"

**Threat Intelligence:**
- URL (VirusTotal): 2/92 flag Suspicious, fresh .xyz domain
- IP (VirusTotal): Criminal IP flags Malicious, AS45090 China

**Log Management:** 0 events for URL, recipient, sender IP → no user interaction

### 🎯 Verdict

**TRUE POSITIVE — Malicious Phishing Email**  
Severity: Medium | Confidence: High

### 🛡️ Actions
1. Classified URL malicious
2. Deleted email from recipient mailbox
3. Logged 3 IOCs (URL, IP, sender)

### 🧭 MITRE ATT&CK
- T1566.002 — Phishing: Spearphishing Link

### 💡 Lessons
1. VT "0 detections" ≠ safe
2. Pyramid of evidence — combine indicators
3. Log Mgmt affects severity, not verdict

---

## Alert #2 — SOC205: Malicious Macro has been executed

**EventID:** 231  
**Difficulty:** Easy  
**Severity:** Medium (corrected to High after C2 confirmed)  
**Date Triaged:** 2026-10-03  
**Analyst:** Muhammad Haris (L1 SOC Trainee)

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC205 - Malicious Macro has been executed |
| Hostname | Jayne (Windows 10, 172.16.171.98) |
| File Hash (SHA256) | `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0` |
| File Name | edit1-invoice.docx |
| File Path | C:\Users\LetsDefend\Downloads\edit1-invoice.docx |
| AV/EDR Action | Detected |

**MITRE:** T1566.001, T1059.001, T1059.003, T1204.002, T1071, T1105, T1571

### 🔍 Investigation

**VirusTotal hash:** 31/65 malicious, family **downloader.logan/w97m**

**Code Insights:** Macro triggers on form control focus, executes hidden shell command.

**Endpoint Security:** Host Jayne, Containment = OFF

**Log Management:** 6 searches, all 0 events initially

### 🎯 Verdict

**TRUE POSITIVE — Malicious Macro (Logan)**  
Severity: **High** | Confidence: High

### ⚠️ Post-Triage Correction

Playbook Score: 10 (75%)

**Wrong Answer:** "Check If Someone Requested the C2" → said "Not Accessed", correct was "Accessed"

**Actual evidence:** PowerShell made GET request to `http://www.greyhathacker.net/tools/messbox.exe`, permitted by proxy.

### 🧠 Lessons Learned — Log Search Protocol

**Search process names FIRST, then IOCs:**
- Tier 1: `powershell.exe`, `cmd.exe`, `wscript.exe`, `cscript.exe`, `mshta.exe`, `rundll32.exe`, `regsvr32.exe`, `wmic.exe`
- Tier 2: File names from alert
- Tier 3: IOCs

---

## Alert #3 — SOC325: Unauthorized Cloud Region Access Attempt

**EventID:** 303  
**Difficulty:** Easy  
**Severity:** Low  
**Date Triaged:** 2026-10-04  
**Analyst:** Muhammad Haris (L1 SOC Trainee)

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC325 - Unauthorized Cloud Region Access Attempt |
| User | test@letsdefend.io |
| Request | POST /accounts/login HTTP/1.1 |
| Response | 403 Forbidden |
| Device Action | Blocked |
| Source IP | 134.209.145.73 |

**MITRE:** T1586, T1078, T1133, T1535

### 🔍 Investigation

**Threat Intel — 134.209.145.73:**
- VirusTotal: 5/91 malicious (DigitalOcean India)
- Vendor flags: BitDefender (Phishing), CRDF (Malicious), Criminal IP (Malicious), G-Data (Phishing), SOCRadar (Phishing)
- CrowdSec comment: "SSH Bruteforce"

**Log Management:** 0 events for source IP + username

### 🎯 Verdict

**TRUE POSITIVE — Unauthorized Access Attempt (Blocked)**  
Severity: Low | Confidence: High

### 💡 Lessons
1. "Device Action: Blocked" → lower severity
2. Cloud IPs need context
3. Not every alert needs deep logs

---

## Alert #4 — SOC276: Account Discovery Attempt Detected

**EventID:** 251  
**Difficulty:** Easy  
**Severity:** Medium (corrected to **CRITICAL** — confirmed compromise)  
**Date Triaged:** 2026-10-06  
**Analyst:** Muhammad Haris (L1 SOC Trainee)

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC276 - Account Discovery Attempt Detected |
| Hostname | VirtuLinux (Ubuntu 20.04, 172.16.17.186) |
| Command | `getent passwd` |
| Alert Type | Unauthorized Access |

**MITRE:** T1078, T1133, T1059.004, T1110, T1087

**L1 Note:** Brute force attempt from `185.107.80.128` observed minutes before — success unknown.

### 🔍 Investigation

**Threat Intel — 185.107.80.128:**
- AbuseIPDB: 25% confidence, **79 reports**, last report 1 day ago
- ASN: AS43350 (NForce Entertainment — VPN)
- Country: Netherlands
- VirusTotal: 0/91

**Log Management (Pro view):**

Search `accepted AND 185.107.80.128` → **3 events:**
| Time | Action | User |
|------|--------|------|
| 08:41:56 | ✅ Accepted password | test |
| 08:41:59 | ✅ Accepted password | analyst |
| 08:42:28 | ✅ Accepted password | analyst |

Search `accepted` → 37 events across environment (broader pattern)

### 🎯 Verdict

**TRUE POSITIVE — CONFIRMED COMPROMISE**  
Severity: **CRITICAL** | Confidence: High

### ⚠️ Playbook Correction

Score: 25 (92%)

**Wrong Answer:** "Determine the Scope" → said "Yes", correct was "No"
**Lesson:** Scope = devices affected by THIS alert's attacker IP only.

### 🛡️ Actions
1. Contained host VirtuLinux
2. Escalated to IR
3. Documented broader 37-event pattern

---

## Alert #5 — SOC312: Unauthorized Template Modification Detected

**EventID:** 290  
**Difficulty:** Medium  
**Severity:** High (confirmed **CRITICAL** — compromise + persistence)  
**Date Triaged:** 2026-10-07  
**Analyst:** Muhammad Haris (L1 SOC Trainee)

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC312 - Unauthorized Template Modification Detected |
| Hostname | Jonah (Windows 10, 172.16.17.110) |
| Process | WINWORD.EXE |
| Command Line | `"C:\Program Files\Microsoft Office\Office16\WINWORD.EXE" /n /f "C:\Users\LetsDefend\AppData\Roaming\Microsoft\Templates\Normal.dotm"` |
| Type | C2 |
| Severity | High |

**MITRE:** T1078, T1133, T1059.005, T1137, T1221, T1110, T1071.001

**L1 Note:**
> "I could not determine whether the command that caused the alert belonged to the attacker. However, I saw a brute force attempt from the IP '181.214.131.108' minutes before the alert occurred."

### 🔍 Investigation

#### Step 1 — Threat Intelligence (Attacker IP: 181.214.131.108)

**AbuseIPDB:**
- Abuse Confidence Score: **11% (Caution)**
- **33 reports** from 16 reporters
- Last report: **1 week ago**
- ISP: Data Center / Web Hosting
- ASN: **AS199218**
- Country: 🇺🇸 USA (New York)

#### Step 2 — Log Management (Pro view)

**Search `Raw Log contains "181.214.131.108"` → 10 events:**

| Time | EventID | Result | User |
|------|---------|--------|------|
| 15:19:08 | 4625 | ❌ Failed | analyst |
| 15:19:09 | 4625 | ❌ Failed | test |
| 15:25:12 | 4625 | ❌ Failed | Atlanta |
| 15:25:13 | 4625 | ❌ Failed | Hitman |
| 15:25:14 | 4625 | ❌ Failed | analyst |
| 15:25:16 | 4625 | ❌ Failed | test |
| **15:27:20** | **4624** | ✅ **SUCCESS** | **User (RDP port 3389)** |

**Search `Raw Log contains "Normal.dotm"` → 1 event:**
- 15:31:02 → WINWORD.EXE launched, PID 6944
- Modified: `C:\Users\LetsDefend\AppData\Roaming\Microsoft\Templates\Normal.dotm`

#### Step 3 — Attack Chain Confirmed

| Step | Time | Action |
|------|------|--------|
| 1 | 15:19-15:25 | RDP brute force (9 failed logins) |
| 2 | 15:27:20 | ✅ Successful RDP login from attacker IP |
| 3 | 15:31:02 | WINWORD modifies Normal.dotm (**4 min later**) |

**Timing correlation = conclusive proof of compromise.**

### 🎯 Verdict

**TRUE POSITIVE — CONFIRMED COMPROMISE + PERSISTENCE**  
Severity: **CRITICAL**  
Confidence: High

**Playbook Score:** 30 / **100% success rate** ⭐

### 🛡️ Actions Taken

| Step | Action |
|------|--------|
| 1 | Confirmed compromise via successful RDP login |
| 2 | Identified Normal.dotm persistence |
| 3 | Contained host Jonah |
| 4 | Logged 2 IOCs (IP + hash) |
| 5 | Wrote full analyst note |
| 6 | Submitted: True Positive |
| 7 | Escalated to IR |

### 📊 IOCs

| # | IOC | Type | Notes |
|---|-----|------|-------|
| 1 | `181.214.131.108` | IP | Attacker — 33 abuse reports |
| 2 | `5D75D0EA8BBBB5B652F7B72CF728C00322BD486D54A5C49...` | Hash | WINWORD.EXE launcher |
| 3 | `Normal.dotm` | File | Modified for persistence |

### 🧭 MITRE ATT&CK

| Tactic | Technique | ID |
|--------|-----------|-----|
| Credential Access | Brute Force | T1110 |
| Defense Evasion | Valid Accounts | T1078 |
| Initial Access | External Remote Services (RDP) | T1133 |
| Persistence | Office Template Macros | T1137.001 |
| Defense Evasion | Template Injection | T1221 |
| Execution | Visual Basic | T1059.005 |
| C2 | Web Protocols | T1071.001 |

### 💡 Lessons Learned

1. **Timing correlation proves causation** — successful login 4 min before template modification
2. **`Normal.dotm` = global template = full persistence** — every document runs the macro
3. **RDP brute force (port 3389) is common in enterprise** — always check successful EventID 4624
4. **100% playbook score is achievable** with methodical investigation

### 🚨 Recommended Actions (IR)

1. URGENT: Reset all user credentials on Jonah
2. URGENT: Restore Normal.dotm from clean backup
3. Block attacker IP 181.214.131.108 at firewall/RDP
4. Disable external RDP — require VPN + MFA
5. Hunt for lateral movement
6. Check for other persistence (Run keys, scheduled tasks, services)
7. Investigate user accounts with successful logins from 181.214.131.108
8. Review logs for data accessed during the 4-minute window

---

*Last updated: October 7, 2026*
