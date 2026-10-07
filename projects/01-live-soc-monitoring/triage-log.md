# Triage Log — Project 01

Detailed writeups of each alert triaged on LetsDefend.

**Progress:** 6 / 10 alerts triaged  
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

**MITRE:** T1078, T1133, T1059.005, T1137, T1221, T1110, T1071.001

**L1 Note:**
> "I could not determine whether the command that caused the alert belonged to the attacker. However, I saw a brute force attempt from the IP '181.214.131.108' minutes before the alert occurred."

### 🔍 Investigation

#### Step 1 — Threat Intelligence (Attacker IP: 181.214.131.108)

**AbuseIPDB:**
- Abuse Confidence Score: **11% (Caution)**
- **33 reports** from 16 reporters
- Last report: **1 week ago**
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
| **15:27:20** | **4624** | ✅ **SUCCESS** | **RDP port 3389** |

**Search `Raw Log contains "Normal.dotm"` → 1 event:**
- 15:31:02 → WINWORD.EXE launched, PID 6944
- Modified: `C:\Users\LetsDefend\AppData\Roaming\Microsoft\Templates\Normal.dotm`

#### Step 3 — Attack Chain Confirmed

| Step | Time | Action |
|------|------|--------|
| 1 | 15:19-15:25 | RDP brute force (9 failed logins) |
| 2 | 15:27:20 | ✅ Successful RDP login |
| 3 | 15:31:02 | WINWORD modifies Normal.dotm (**4 min later**) |

**Timing correlation = conclusive proof of compromise.**

### 🎯 Verdict

**TRUE POSITIVE — CONFIRMED COMPROMISE + PERSISTENCE**  
Severity: **CRITICAL**  
Confidence: High

**Playbook Score:** 30 / **100% success rate** ⭐

### 🛡️ Actions Taken
1. Confirmed compromise via successful RDP login
2. Identified Normal.dotm persistence
3. Contained host Jonah
4. Logged 2 IOCs (IP + hash)

### 💡 Lessons Learned
1. **Timing correlation proves causation**
2. **`Normal.dotm` = full persistence**
3. **RDP brute force (port 3389) is common in enterprise**

---

## Alert #6 — SOC293: Exfiltration Over Pastebin Detected

**EventID:** 269  
**Difficulty:** Medium  
**Severity:** High (confirmed **CRITICAL** — data exfiltration)  
**Date Triaged:** 2026-10-07  
**Analyst:** Muhammad Haris (L1 SOC Trainee)

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC293 - Exfiltration Over Pastebin Detected |
| Hostname | **Gabriela** (Windows 10, 172.16.17.63) |
| File Name | `system_users.ps1` |
| File Path | `C:\Users\LetsDefend\Downloads\quick-fix\system_users.ps1` |
| Command | `powershell.exe -ExecutionPolicy Bypass -File .\system_users.ps1` |
| Device Action | **Allowed** ⚠️ |

**MITRE:** T1033 (System Owner/User Discovery), T1567 (Exfiltration Over Web Service)

**L1 Note:**
> "PowerShell script 'system_users.ps1' connects to an external URL (pastebin.com). I'm escalating this alert for further analysis to determine the root cause and if it is malicious."

### 🔍 Investigation

#### Step 1 — Email Origin (Phishing Email)

**From:** `info@dachfix.com`  
**To:** `Gabriela@letsdefend.io`  
**Subject:** "Download and Apply the Critical Fix for Device Issues"  
**Sender IP:** `103.145.252.87`  
**Date:** 2024-06-26 08:14:00  
**Attachment:** `Quick-Fix.zip`

**Phishing red flags:**
- Urgency ("Immediate Action Needed")
- Fear ("Failure to do so may result in performance issues")
- Generic greeting ("Dear Team")
- External sender (dachfix.com)
- Malicious attachment

**Sender IP reputation (VirusTotal):**
- **8/92 vendors flag MALICIOUS**
- BitDefender → Phishing
- Emisoft → Malware
- Fortinet → Malware
- G-Data → Phishing
- Country: 🇻🇳 Vietnam
- ASN: AS135905

#### Step 2 — File Download from Cloud

**Log Management:**
- Time: 2024-06-26 09:15:39
- Process: `chrome.exe`
- URL: `https://files-ld.s3.us-east-2.amazonaws.com/quick-zip.fix`
- Destination: `3.5.128.11:443` (AWS S3)
- Device Action: Allowed

**File Hash:** `2f2d8121d6b351a32a5c55995450200f3cafd3d26b2cf5f646cd3a80f175450e`
- VirusTotal: 1/51 detections
- **Popular Threat Label:** `lnkscript`
- Tags: `detect-debug-environment`, `long-sleeps`, `checks-user-input`
- Family: LNKScript

#### Step 3 — PowerShell Execution (Discovery)

**Log Management:**
- Time: 2024-06-26 09:16:40
- Process: `powershell.exe`
- Command: `-ExecutionPolicy Bypass -File .\system_users.ps1`
- **T1033 — System Owner/User Discovery** (script enumerated system users)

#### Step 4 — Data Exfiltration to Pastebin

**Log Management — DNS:**
- Time: 2024-06-26 09:16:40
- Type: DNS Query
- Source: `172.16.17.63:32522`
- Destination: `104.20.3.235:53`
- QueryName: `pastebin.com`

**Log Management — Firewall:**
- Time: 2024-06-26 09:16:40
- Source: `172.16.17.63`
- Destination: `104.20.3.235:443` (HTTPS)
- Image: `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe`
- Device Action: **ALLOWED** → Data left network

#### Step 5 — Full Attack Chain

| Time | Event |
|------|-------|
| 08:14:00 | Phishing email delivered |
| 09:15:39 | Chrome downloads `quick-zip.fix` from AWS S3 |
| 09:16:40 | PowerShell runs `system_users.ps1` with bypass |
| 09:16:40 | DNS query for `pastebin.com` |
| 09:16:40 | HTTPS upload to Pastebin → **DATA LEAKED** |

**Total: ~62 minutes from phishing to exfiltration.**

### 🎯 Verdict

**TRUE POSITIVE — CONFIRMED DATA EXFILTRATION**  
Severity: **CRITICAL**  
Confidence: High

### 📊 IOCs

| # | IOC | Type | Context |
|---|-----|------|---------|
| 1 | `info@dachfix.com` | E-mail Sender | Phishing sender |
| 2 | `103.145.252.87` | IP | Sender SMTP — 8/92 malicious, Vietnam |
| 3 | `dachfix.com` | E-mail Domain | Phishing domain |
| 4 | `https://files-ld.s3.us-east-2.amazonaws.com/quick-zip.fix` | URL | Malware download |
| 5 | `2f2d8121d6b351a32a5c55995450200f3cafd3d26b2cf5f646cd3a80f175450e` | SHA256 | Malicious ZIP (lnkscript) |
| 6 | `quick-fix.zip` / `quick-zip.fix` | File | Deceptive delivery filename |
| 7 | `system_users.ps1` | File | Malicious script |
| 8 | `pastebin.com` | Domain | Exfiltration destination |
| 9 | `104.20.3.235` | IP | Pastebin/Cloudflare |

### 🧭 MITRE ATT&CK

| Tactic | Technique | ID |
|--------|-----------|-----|
| Initial Access | Phishing: Spearphishing Attachment | T1566.001 |
| Execution | User Execution: Malicious File | T1204.002 |
| Execution | PowerShell | T1059.001 |
| Persistence | Shortcut Modification (LNKScript) | T1547.009 |
| Discovery | System Owner/User Discovery | T1033 |
| Exfiltration | Exfiltration Over Web Service | T1567.002 |

### 🛡️ Actions Taken
1. Confirmed phishing email as delivery vector
2. Traced malicious download from AWS S3
3. Confirmed PowerShell execution with bypass
4. Confirmed data upload to Pastebin
5. Contained host Gabriela

### 💡 Lessons Learned
1. **Phishing emails with "urgent fix" themes are high-risk**
2. **"quick-fix" and similar filenames are social engineering lures**
3. **LNKScript family uses shortcut files for execution**
4. **Pastebin is a common exfiltration destination** — always check outbound HTTPS to pastebin.com
5. **Chrome.exe downloads can still be malicious** — user-initiated ≠ safe

---

*Last updated: October 7, 2026*
