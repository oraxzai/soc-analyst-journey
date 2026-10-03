# Triage Log — Project 01

Detailed writeups of each alert triaged on LetsDefend.

**Progress:** 2 / 10 alerts triaged  
**Last Updated:** October 3, 2026

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

**Interpretation:** URL is fresh — not yet on blocklists. But 2 reputable vendors flag it Suspicious. Fresh `.xyz` domains with random names match phishing kit patterns.

**IP Analysis — `146.56.195.192`**

| Metric | Result |
|--------|--------|
| Community Score | 10 |
| Criminal IP | 🔴 Malicious |
| 1/91 vendors | Malicious |
| Geolocation | 🇨🇳 China |
| ASN | AS45090 (Shenzhen Tencent Cloud) |

**Interpretation:** Sender IP is cloud-hosted in China and flagged by Criminal IP. Cloud-hosted IPs are commonly used by attackers because they're cheap and disposable.

#### Step 3 — Log Management (User Interaction Check)

Searched Log Management for evidence of user interaction:

| Search Query | Result |
|--------------|--------|
| `huangaybantiep.xyz` | **0 events found** |
| `mark@letsdefend.io` | **0 events found** |
| `146.56.195.192` | **0 events found** |

**Interpretation:** No user clicked the URL. No endpoint accessed the malicious domain. No network traffic to the sender IP. **No compromise occurred.**

---

### 🎯 Verdict

**Classification:** ✅ **TRUE POSITIVE — Malicious Phishing Email**  
**Severity:** Medium  
**Confidence:** High

**Reasoning:**
Every indicator points to malicious intent — random-numbered Gmail sender, China cloud IP flagged as malicious, fresh `.xyz` non-HTTPS URL, generic clickbait subject and body. The lack of user interaction in logs confirms no compromise occurred, but this does NOT reduce the verdict. The email is confirmed malicious.

---

### 🛡️ Actions Taken (Playbook Execution)

| Step | Action |
|------|--------|
| 1 | Classified URL as Malicious |
| 2 | Confirmed mail delivered to user (Device Action = Allowed) |
| 3 | Deleted email from recipient mailbox |
| 4 | Verified no one opened the URL (Log Management) |
| 5 | Logged 3 IOCs as incident artifacts |
| 6 | Wrote analyst note summary |
| 7 | Submitted final verdict: True Positive |

**IOCs Logged:**

| # | Value | Type | Comment |
|---|-------|------|---------|
| 1 | `http://huangaybantiep.xyz` | URL Address | Phishing URL in email body |
| 2 | `146.56.195.192` | IP Address | Sender SMTP IP, flagged by Criminal IP |
| 3 | `lethuyan852@gmail.com` | E-mail Sender | Random-numbered Gmail sender |

**Recommended Downstream Actions:**
- Block URL at proxy/DNS
- Block sender IP at email gateway
- Block sender email in Exchange/M365
- Notify recipient not to interact with similar emails

---

### 🧭 MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|--------|-----------|-----|
| Initial Access | Phishing: Spearphishing Link | **T1566.002** |

---

### 📸 Evidence

| # | Screenshot | Description |
|---|-----------|-------------|
| 1 | `01-alert-queue-beginner-filtered.png` | Filtered alert queue |
| 2 | `01-alert-087-details.png` | Alert details (EventID 87) |
| 3 | `01-alert-087-email-body.png` | Phishing email content |
| 4 | `01-alert-087-virustotal-url.png` | VirusTotal URL report |
| 5 | `01-alert-087-virustotal-ip.png` | VirusTotal IP report |
| 6 | `01-alert-087-log-search-url.png` | Log Mgmt: URL = 0 events |
| 7 | `01-alert-087-log-search-recipient.png` | Log Mgmt: recipient = 0 events |
| 8 | `01-alert-087-log-search-ip.png` | Log Mgmt: IP = 0 events |
| 9 | `01-alert-087-playbook-artifacts.png` | 3 IOCs logged as artifacts |
| 10 | `01-alert-087-playbook-analyst-note.png` | Investigation summary |
| 11 | `01-alert-087-closed-confirmed.png` | ✅ Closed with checkmark |

---

### 💡 Lessons Learned

1. **VirusTotal "0 detections" ≠ safe** — freshness and vendor flags matter. A brand-new phishing domain won't yet be on blocklists.
2. **Pyramid of evidence** — no single indicator was conclusive. The combined correlation (random Gmail + China IP + fresh .xyz + non-HTTPS + generic content) built a strong case.
3. **Log Management affects severity, not verdict** — no user click reduced severity (no endpoint response needed) but didn't change the True Positive verdict.
4. **IOC type classification matters** — URL Address vs IP Address vs E-mail Sender — correct categorization ensures downstream systems block the right thing.
5. **Playbook workflows mirror real SOC platforms** — Splunk SOAR, Cortex XSOAR, and Microsoft Sentinel all use similar structured response steps.

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
| Trigger Reason | Suspicious file detected on system |

**MITRE ATT&CK (7 techniques identified by the alert):**

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
| Community Score | 2 |
| Popular Threat Label | **downloader.logan/w97m** |
| Threat Categories | downloader, trojan |
| Family Labels | logan, w97m, tl0101n26zz |
| File Size | 23.21 KB |
| File Type | Office Open XML Document (.docx) |
| First Submission | 2017-01-26 |

**Code Insights (VirusTotal automated analysis):**
> The document contains a macro in `ThisDocument.cls` that triggers when the InkEdit control named `GBjdshu1KJ` receives focus. The `InkEdit1_GotFocus` subroutine executes a shell command retrieved from the `TextBox1` control located on `UserForm1`. The shell command is executed with the window style set to 0 (hidden window).

**New IOCs identified during investigation:**

| IOC | Type | Source |
|-----|------|--------|
| `92.204.221.16` | C2 IP | VirusTotal (from urlscan.io report) |
| `heg.com` | C2 Domain | VirusTotal Relations |
| `greyhathacker.net` | Dropper Domain | urlscan.io / VT Community |
| `messbox.exe` | Dropped Payload | urlscan.io / playbook feedback |

#### Step 2 — Endpoint Security Check

| Field | Value |
|-------|-------|
| Hostname | Jayne |
| OS | Windows 10 (64-bit) |
| Primary User | LetsDefend |
| Client/Server | Server |
| Last Login | 2024-02-28 21:43:07 |
| **Containment** | **OFF** ⚠️ |

**Finding:** Host is identified but not isolated. Not confirmed clean.

#### Step 3 — Log Management Investigation (6 searches)

| Search Query | Result |
|--------------|--------|
| `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0` | 0 events |
| `edit1-invoice.docx` | 0 events |
| `92.204.221.16` | 0 events |
| `heg.com` | 0 events |
| `greyhathacker.net` | 0 events |
| `Jayne` | 0 events |

**Initial Conclusion:** No evidence of execution, C2, or payload download.

**⚠️ This conclusion was later found to be INCOMPLETE — see post-triage correction below.**

---

### 🎯 Verdict

**Classification:** ✅ **TRUE POSITIVE — Malicious Macro (Logan Downloader Family)**  
**Severity (revised):** **High**  
**Confidence:** High

**Reasoning:**
VirusTotal confirms 31/65 vendors flag the file as malicious (Logan downloader family). The macro is designed to execute a hidden shell command from a fake form control — a classic macro dropper pattern. No containment was applied to the host. Playbook feedback later confirmed C2 access — see correction below.

---

### ⚠️ Post-Triage Correction (Playbook Feedback)

**Playbook Score:** 10 / 75% success rate

**Incorrect Answer:**

| Question | My Answer | Correct Answer |
|----------|-----------|----------------|
| Check If Someone Requested the C2 | ❌ Not Accessed | ✅ **Accessed** |

**Actual Evidence (revealed by playbook after submission):**
> "At 08:42 AM, a GET request via **powershell.exe** to `HTTP://WWW.GREYHATHACKER.NET/TOOLS/MESSBOX.EXE` was detected in the **Proxy log**. The device action is seen as **"permit"** in the proxy."

**Interpretation:**
- The malware **did** spawn PowerShell
- PowerShell made an HTTP GET request to `http://www.greyhathacker.net/tools/messbox.exe`
- The proxy **permitted** the request (not blocked)
- The second-stage payload (messbox.exe) was **successfully downloaded**

**Full attack chain confirmed:**
```
Phishing email → edit1-invoice.docx opened → Macro executed → 
PowerShell spawned → GET request to greyhathacker.net/tools/messbox.exe → 
messbox.exe downloaded → Second stage active
```

---

### 🧠 Lessons Learned — Log Search Protocol

**The Gap:**
I searched Log Management by IOCs only (hash, filename, C2 IP, C2 domain, dropper domain, hostname) — all returned 0 events. But the actual log entry existed — indexed by **process name** (`powershell.exe`), which I did not search for.

**What I Should Have Done:**

For any malware alert, search Log Management for common malware execution processes **first**, before searching by IOCs:

**Tier 1 — Process Names (search first):**
- `powershell.exe`
- `cmd.exe`
- `wscript.exe`
- `cscript.exe`
- `mshta.exe`
- `rundll32.exe`
- `regsvr32.exe`
- `wmic.exe`

**Tier 2 — File Names from Alert**

**Tier 3 — IOCs (Hash, C2 IP, Domain, Full URL)**

**Why This Order:**
- Log entries are often indexed by process name
- Malware uses predictable processes — searchable proactively
- IOCs may not be indexed as you expect

**Permanent Rule:** Never search by a single angle. Search by process, filename, full URL, domain, IP — in that order.

---

### 🛡️ Recommended Actions (Escalated to IR)

| Priority | Action |
|----------|--------|
| **Critical** | Confirm C2 connection was active — PowerShell downloaded `messbox.exe` |
| **Critical** | Isolate host `Jayne` immediately (Containment = OFF) |
| **High** | Check for second-stage execution and persistence (Run keys, scheduled tasks, services) |
| **High** | Block IOCs at network gateway: `92.204.221.16`, `heg.com`, `greyhathacker.net` |
| **Medium** | Notify user "LetsDefend" not to open unknown attachments |
| **Medium** | Check for lateral movement from host Jayne |

**IOCs Logged:**

| # | Value | Type | Comment |
|---|-------|------|---------|
| 1 | `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0` | MD5 Hash | Malicious macro file (VT 31/65) |
| 2 | `92.204.221.16` | IP Address | C2 IP — host of dropped payload |
| 3 | `heg.com` | E-mail Domain | C2 domain |
| 4 | `greyhathacker.net` | E-mail Domain | Dropper domain hosting messbox.exe |

**Note:** Filename `edit1-invoice.docx` was not logged as IOC — doesn't fit available artifact types. Rationale: filename alone can't be blocked at network gateways; the file's hash is the proper IOC.

---

### 🧭 MITRE ATT&CK Mapping (Confirmed)

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

### 📸 Evidence

| # | Screenshot | Description |
|---|-----------|-------------|
| 1 | `02-alert-231-details.png` | Alert details (EventID 231) |
| 2 | `02-alert-231-virustotal-hash.png` | VT hash report (31/65 malicious) |
| 3 | `02-alert-231-code-insights.png` | Macro code analysis |
| 4 | `02-alert-231-iocs.png` | IOCs identified (C2 IP, domain) |
| 5 | `02-alert-231-endpoint-info.png` | Endpoint Security — host Jayne |
| 6 | `02-alert-231-log-filehash.png` | Log Mgmt: file hash search |
| 7 | `02-alert-231-log-filename.png` | Log Mgmt: filename search |
| 8 | `02-alert-231-log-c2ip.png` | Log Mgmt: C2 IP search |
| 9 | `02-alert-231-log-dropper.png` | Log Mgmt: dropper domain search |
| 10 | `02-alert-231-log-host.png` | Log Mgmt: hostname search |
| 11 | `02-alert-231-playbook-artifacts.png` | 4 IOCs logged as artifacts |
| 12 | `02-alert-231-playbook-analyst-note.png` | Investigation summary (2,913 chars) |
| 13 | `02-alert-231-playbook-result.png` | Playbook result — 75% + C2 access correction |

---

### 🔑 Key Takeaway

**Log search is multi-angle.** Process names matter more than IOCs. This is the #1 lesson from Alert #2.

Searching only by IOCs (hash, IP, domain) missed the crucial proxy log entry that was indexed by `powershell.exe`. In real SOC work, log indexing varies — always search broadly.

---

*Last updated: October 3, 2026*
