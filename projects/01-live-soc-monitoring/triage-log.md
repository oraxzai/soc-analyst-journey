# Triage Log — Project 01

Detailed writeups of each alert triaged on LetsDefend.

**Progress:** 4 / 10 alerts triaged  
**Last Updated:** October 6, 2026

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

**Wrong Answer:** "Check If Someone Requested the C2" → I said "Not Accessed", correct was "Accessed"

**Actual evidence:** PowerShell made GET request to `http://www.greyhathacker.net/tools/messbox.exe`, permitted by proxy.

### 🧠 Lessons Learned — Log Search Protocol

**Search process names FIRST, then IOCs:**
- Tier 1: `powershell.exe`, `cmd.exe`, `wscript.exe`, `cscript.exe`, `mshta.exe`, `rundll32.exe`, `regsvr32.exe`, `wmic.exe`
- Tier 2: File names from alert
- Tier 3: IOCs (hash, IP, domain, URL)

**Rule:** Search process names before IOCs. Always.

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
| Destination IP | 52.15.206.21 |

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
2. Cloud IPs need context (5+ detections = confirmed malicious)
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
| MITRE | T1078, T1133, T1059.004, T1110, T1087 |

**L1 Note (from previous analyst):**
> "Minutes before the alert, I saw a Brute Force attempt with different users from the IP 185.107.80.128 towards the system. However, I could not determine whether this attack was successful or not."

### 🔍 Investigation

#### Step 1 — Threat Intel: Attacker IP (185.107.80.128)

**AbuseIPDB:**
- Abuse Confidence Score: **25% (Elevated)**
- **79 reports** from 36 reporters
- Last report: **1 day ago**
- ISP: Serverhosting / Data Center
- ASN: **AS43350** (NForce Entertainment B.V. — VPN provider)
- Country: Netherlands (Breda)

**VirusTotal:** 0/91 detections (VPN provider — reputation hidden)

#### Step 2 — Log Management (Pro View — CRITICAL FINDING)

**Search: `Raw Log contains "accepted" AND Source Address contains "185.107.80.128"` → 3 events found:**

| Timestamp | Action | User | Port |
|-----------|--------|------|------|
| 2024-04-25 08:41:56 | ✅ **Accepted password** | test | 39131 |
| 2024-04-25 08:41:59 | ✅ **Accepted password** | analyst | 56175 |
| 2024-04-25 08:42:28 | ✅ **Accepted password** | analyst | 9239 |

**Search: `Raw Log contains "185.107.80.128"` → 8 events found:**
- Multiple failed passwords for: test, admin, analyst, letsdefend, kali
- 3 successful logins (above)

**Search: `Raw Log contains "accepted"` → 37 events across environment:**
- Multiple `analyst` logins from different external IPs to different hosts
- Suggests broader compromise pattern

### 🎯 Verdict

**TRUE POSITIVE — CONFIRMED COMPROMISE**  
Severity: **CRITICAL**  
Confidence: High

**The L1 Note's question is answered: The brute force SUCCEEDED.**

### 🛡️ Actions Taken
1. Confirmed compromise via 3 Accepted password events
2. Contained host VirtuLinux (Containment = ON)
3. Escalated to IR
4. Documented broader pattern (37 events)

### 📊 IOCs

| IOC | Type | Notes |
|-----|------|-------|
| `185.107.80.128` | IP | Attacker — 79 abuse reports |
| `analyst` | Compromised account | 2 successful logins |
| `test` | Compromised account | 1 successful login |
| `172.16.17.186` | Target host | VirtuLinux |

### ⚠️ Playbook Correction

Playbook Score: 25 (92%)

**Wrong Answer:** "Determine the Scope" → I said "Yes", correct was "No"

**Correct logic:** Scope = devices affected by THIS alert's attacker IP (185.107.80.128). Only VirtuLinux was hit by this IP. The 37-event pattern involves OTHER IPs and is a separate finding.

### 💡 Lessons Learned

1. **Pro view > Basic view** — Basic returned 0 events, Pro found the smoking gun
2. **AbuseIPDB + VirusTotal together** — VT said "clean", AbuseIPDB showed 79 reports
3. **Scope questions = THIS alert's indicators only** — broader patterns go in notes, not scope answers
4. **"Accepted password" in SSH logs = compromise confirmed**

### 🚨 Recommended Actions (IR)
1. URGENT: Isolate VirtuLinux (done)
2. URGENT: Reset credentials for `analyst` and `test`
3. Disable SSH password auth — enforce key-based auth
4. Block 185.107.80.128 at firewall
5. Investigate broader pattern (37 events)
6. Check for persistence, lateral movement, data exfiltration
7. Deploy fail2ban / SSH rate limiting
8. Enable auditd for command logging

---

*Last updated: October 6, 2026*
