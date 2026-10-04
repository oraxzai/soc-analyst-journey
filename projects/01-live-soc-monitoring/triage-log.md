# Triage Log — Project 01

Detailed writeups of each alert triaged on LetsDefend.

**Progress:** 3 / 10 alerts triaged  
**Last Updated:** October 4, 2026

---

## Alert #1 — SOC101: Phishing Mail Detected

**EventID:** 87  
**Difficulty:** Beginner  
**Severity:** Medium  
**Date Triaged:** 2026-10-02  
**Analyst:** Muhammad Haris (L1 SOC Trainee)  
**Time Invested:** ~2 hours

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC101 - Phishing Mail Detected |
| Event Time | 2021-04-04 23:00:15 +03:00 |
| Alert Type | Exchange (Email) |
| SMTP Address | 146.56.195.192 |
| Device Action | **Allowed** (email delivered) |
| Email Subject | "Its a Must have for your Phone" |
| Sender | lethuyan852@gmail.com |
| Recipient | mark@letsdefend.io |
| Embedded URL | http://huangaybantiep.xyz |

---

### 🔍 Investigation

#### Step 1 — Email Content Analysis

Retrieved the full email body from **Email Security**:

> "Check out this product! Your life will be less difficult. http://huangaybantiep.xyz"

**Red flags identified:**
- Non-HTTPS URL (`http://` not `https://`)
- `.xyz` TLD — heavily abused by phishing campaigns
- Random gibberish domain name (`huangaybantiep`)
- No product name — generic bait
- Grammatically awkward body text

#### Step 2 — Threat Intelligence (VirusTotal)

**URL Analysis — `http://huangaybantiep.xyz`**

| Metric | Result |
|--------|--------|
| Community Score | 0 / 92 |
| Forcepoint ThreatSeeker | ⚠️ Suspicious |
| LevelBlue | ⚠️ Suspicious |
| Other 90 vendors | ✅ Clean |
| First Analysis | Fresh (newly registered) |

**IP Analysis — `146.56.195.192`**

| Metric | Result |
|--------|--------|
| Community Score | 10 |
| Criminal IP | 🔴 Malicious |
| 1/91 vendors | Malicious |
| Geolocation | 🇨🇳 China |
| ASN | AS45090 (Shenzhen Tencent Cloud) |

#### Step 3 — Log Management (User Interaction Check)

| Search Query | Result |
|--------------|--------|
| `huangaybantiep.xyz` | **0 events found** |
| `mark@letsdefend.io` | **0 events found** |
| `146.56.195.192` | **0 events found** |

**Interpretation:** No user clicked the URL. No compromise occurred.

---

### 🎯 Verdict

**Classification:** ✅ **TRUE POSITIVE — Malicious Phishing Email**  
**Severity:** Medium  
**Confidence:** High

---

### 🛡️ Actions Taken (Playbook Execution)

| Step | Action |
|------|--------|
| 1 | Classified URL as Malicious |
| 2 | Confirmed mail delivered to user |
| 3 | Deleted email from recipient mailbox |
| 4 | Verified no one opened the URL |
| 5 | Logged 3 IOCs as incident artifacts |
| 6 | Wrote analyst note summary |
| 7 | Submitted final verdict: True Positive |

**IOCs Logged:**

| # | Value | Type | Comment |
|---|-------|------|---------|
| 1 | `http://huangaybantiep.xyz` | URL Address | Phishing URL in email body |
| 2 | `146.56.195.192` | IP Address | Sender SMTP IP |
| 3 | `lethuyan852@gmail.com` | E-mail Sender | Random Gmail sender |

---

### 🧭 MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|--------|-----------|-----|
| Initial Access | Phishing: Spearphishing Link | **T1566.002** |

---

### 💡 Lessons Learned

1. **VirusTotal "0 detections" ≠ safe** — freshness and vendor flags matter
2. **Pyramid of evidence** — combined correlation beats single indicators
3. **Log Management affects severity, not verdict**
4. **IOC type classification matters**
5. **Playbooks mirror enterprise SOC tools** — Splunk SOAR, Cortex XSOAR, Sentinel

---

## Alert #2 — SOC205: Malicious Macro has been executed

**EventID:** 231  
**Difficulty:** Easy  
**Severity:** Medium (corrected to **High** after C2 access confirmed)  
**Date Triaged:** 2026-10-03  
**Analyst:** Muhammad Haris (L1 SOC Trainee)  
**Time Invested:** ~2.5 hours

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC205 - Malicious Macro has been executed |
| Event Time | 2024-02-28T08:42:02+03:00 |
| Alert Type | Malware |
| Hostname | Jayne |
| Domain | LetsDefend |
| IP Address | 172.16.171.98 (internal) |
| OS | Windows 10 (64-bit) |
| File Hash (SHA256) | `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0` |
| File Name | `edit1-invoice.docx` |
| File Path | `C:\Users\LetsDefend\Downloads\edit1-invoice.docx` |
| AV/EDR Action | Detected |

**MITRE ATT&CK (7 techniques):**

| Tactic | Technique | ID |
|--------|-----------|-----|
| Initial Access | Phishing: Spearphishing Attachment | T1566.001 |
| Execution | PowerShell | T1059.001 |
| Execution | Windows Command Shell | T1059.003 |
| Execution | User Execution: Malicious File | T1204.002 |
| Command & Control | Application Layer Protocol | T1071 |
| Command & Control | Ingress Tool Transfer | T1105 |
| Command & Control | Non-Standard Port | T1571 |

---

### 🔍 Investigation

#### Step 1 — VirusTotal Hash Analysis

| Metric | Result |
|--------|--------|
| Detection Ratio | **31 / 65 vendors flag MALICIOUS** |
| Popular Threat Label | **downloader.logan/w97m** |
| Threat Categories | downloader, trojan |
| Family Labels | logan, w97m, tl0101n26zz |
| File Size | 23.21 KB |
| File Type | Office Open XML Document (.docx) |

**Code Insights (VirusTotal):**
> The document contains a macro in `ThisDocument.cls` that triggers when the InkEdit control named `GBjdshu1KJ` receives focus. The `InkEdit1_GotFocus` subroutine executes a shell command retrieved from `TextBox1` on `UserForm1`, with window style 0 (hidden window).

**New IOCs identified:**

| IOC | Type | Source |
|-----|------|--------|
| `92.204.221.16` | C2 IP | VirusTotal |
| `heg.com` | C2 Domain | VirusTotal |
| `greyhathacker.net` | Dropper Domain | urlscan.io |
| `messbox.exe` | Dropped Payload | Playbook feedback |

#### Step 2 — Endpoint Security Check

| Field | Value |
|-------|-------|
| Hostname | Jayne |
| OS | Windows 10 (64-bit) |
| Primary User | LetsDefend |
| **Containment** | **OFF** ⚠️ |

#### Step 3 — Log Management Investigation (6 searches)

| Search Query | Result |
|--------------|--------|
| `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0` | 0 events |
| `edit1-invoice.docx` | 0 events |
| `92.204.221.16` | 0 events |
| `heg.com` | 0 events |
| `greyhathacker.net` | 0 events |
| `Jayne` | 0 events |

**⚠️ This conclusion was later found INCOMPLETE — see post-triage correction below.**

---

### 🎯 Verdict

**Classification:** ✅ **TRUE POSITIVE — Malicious Macro (Logan Downloader)**  
**Severity (revised):** **High**  
**Confidence:** High

---

### ⚠️ Post-Triage Correction (Playbook Feedback)

**Playbook Score:** 10 / 75% success rate

**Incorrect Answer:**

| Question | My Answer | Correct Answer |
|----------|-----------|----------------|
| Check If Someone Requested the C2 | ❌ Not Accessed | ✅ **Accessed** |

**Actual Evidence (revealed by playbook):**
> "At 08:42 AM, a GET request via **powershell.exe** to `HTTP://WWW.GREYHATHACKER.NET/TOOLS/MESSBOX.EXE` was detected in the **Proxy log**. The device action is seen as **"permit"** in the proxy."

**Full attack chain confirmed:**
```
Phishing email → edit1-invoice.docx opened → Macro executed → 
PowerShell spawned → GET request to greyhathacker.net/tools/messbox.exe → 
messbox.exe downloaded → Second stage active
```

---

### 🧠 Lessons Learned — Log Search Protocol

**The Gap:** I searched Log Management by IOCs only — all returned 0 events. But the actual log entry existed — indexed by **process name** (`powershell.exe`), which I didn't search for.

**What I Should Have Done:**

**Tier 1 — Process Names (search first):**
- `powershell.exe`, `cmd.exe`, `wscript.exe`, `cscript.exe`
- `mshta.exe`, `rundll32.exe`, `regsvr32.exe`, `wmic.exe`

**Tier 2 — File Names from Alert**

**Tier 3 — IOCs (Hash, C2 IP, Domain, Full URL)**

**Permanent Rule:** Never search by a single angle. Search process names first.

---

### 🛡️ Recommended Actions (Escalated)

| Priority | Action |
|----------|--------|
| Critical | Isolate host Jayne (Containment = OFF) |
| Critical | Confirm C2 connection — PowerShell downloaded messbox.exe |
| High | Check for second-stage execution and persistence |
| High | Block IOCs: 92.204.221.16, heg.com, greyhathacker.net |
| Medium | Notify user, check lateral movement |

---

## Alert #3 — SOC325: Unauthorized Cloud Region Access Attempt

**EventID:** 303  
**Difficulty:** Easy  
**Severity:** Low  
**Date Triaged:** 2026-10-04  
**Analyst:** Muhammad Haris (L1 SOC Trainee)  
**Time Invested:** ~1 hour

---

### 📋 Alert Overview

| Field | Value |
|-------|-------|
| Rule | SOC325 - Unauthorized Cloud Region Access Attempt Detected |
| Event Time | 2024-09-24T08:21:15+03:00 |
| Alert Type | Web Attack |
| User Targeted | test@letsdefend.io |
| Request URL | POST /accounts/login HTTP/1.1 |
| Response | 403 Forbidden |
| Device Action | **Blocked** ✅ |
| Source Address | 134.209.145.73 |
| Destination Address | 52.15.206.21 |
| Trigger Reason | Too many access attempts with same user from unauthorized cloud region |

**MITRE ATT&CK:**

| Tactic | Technique | ID |
|--------|-----------|-----|
| Resource Development | Compromise Accounts | T1586 |
| Defense Evasion | Valid Accounts | T1078 |
| Initial Access | External Remote Services | T1133 |
| Defense Evasion | Unused/Unsupported Cloud Regions | T1535 |

---

### 🔍 Investigation

#### Step 1 — Alert Analysis

- Same user (`test@letsdefend.io`) targeted with **multiple login attempts**
- Traffic from an **unauthorized cloud region**
- Request: `POST /accounts/login`
- Response: **403 Forbidden** — server rejected
- Device Action: **Blocked**

#### Step 2 — Threat Intelligence (VirusTotal)

**Source IP: `134.209.145.73`**

| Metric | Result |
|--------|--------|
| Detection Ratio | **5 / 91 vendors flag MALICIOUS** |
| Community Score | -2 |
| ASN | AS14061 (DigitalOcean, LLC) |
| Geolocation | 🇮🇳 India |

**Vendor flags:**
- BitDefender → Phishing
- CRDF → Malicious
- Criminal IP → Malicious
- G-Data → Phishing
- SOCRadar → Phishing
- AlphaSOC → Suspicious
- Gridinsoft → Suspicious

**Community comment:** CrowdSec noted "SSH Bruteforce" behavior.

#### Step 3 — Log Management Investigation

| Search Query | Result |
|--------------|--------|
| `134.209.145.73` (source IP) | 0 events found |
| `test@letsdefend.io` (targeted user) | 0 events found |

**Interpretation:** Device Action = "Blocked" confirms the security control prevented the request. No downstream activity logged.

---

### 🎯 Verdict

**Classification:** ✅ **TRUE POSITIVE — Unauthorized Access Attempt (Blocked)**  
**Severity:** Low  
**Confidence:** High

**Reasoning:**
Source IP is a confirmed malicious cloud IP with documented brute-force history. Attack targeted a real user from an unauthorized cloud region but was blocked by controls. No compromise occurred. Severity is Low because the request was blocked.

---

### 🛡️ Actions Taken

| Step | Action |
|------|--------|
| 1 | Validated source IP as malicious via VirusTotal |
| 2 | Confirmed 403 Forbidden = blocked |
| 3 | Verified Device Action = Blocked |
| 4 | Searched Log Management (0 events) |
| 5 | Submitted verdict: True Positive (Blocked) |

---

### 📊 IOCs

| # | IOC | Type | Comment |
|---|-----|------|---------|
| 1 | `134.209.145.73` | IP Address | Malicious source — DigitalOcean, India |

**Recommended Actions:**
- Block source IP at WAF/edge
- Monitor test@letsdefend.io for follow-ups
- Alert on DigitalOcean IPs hitting login endpoints
- Review unauthorized cloud region policy

---

### 💡 Lessons Learned

1. **"Device Action: Blocked" is your best friend** — attack failed, severity drops
2. **Cloud IPs require context** — not inherently bad, but with 5+ detections + bruteforce history → confirmed malicious
3. **Not every alert needs deep logs** — sometimes the block signal is conclusive
4. **Light triage days are valid** — 1-hour alert with solid docs is a real portfolio piece

---

*Last updated: October 4, 2026*
