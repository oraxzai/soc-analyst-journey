# Triage Log — Project 01

Detailed writeups of each alert triaged on LetsDefend.

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
| 10 | `01-alert-087-playbook-analyst-note.png` | Investigation summary (1693 chars) |
| 11 | `01-alert-087-closed-confirmed.png` | ✅ Closed with checkmark |

---

### 💡 Lessons Learned

1. **VirusTotal "0 detections" ≠ safe** — freshness and vendor flags matter. A brand-new phishing domain won't yet be on blocklists.
2. **Pyramid of evidence** — no single indicator was conclusive. The combined correlation (random Gmail + China IP + fresh .xyz + non-HTTPS + generic content) built a strong case.
3. **Log Management affects severity, not verdict** — no user click reduced severity (no endpoint response needed) but didn't change the True Positive verdict.
4. **IOC type classification matters** — URL Address vs IP Address vs E-mail Sender — correct categorization ensures downstream systems block the right thing.
5. **Playbook workflows mirror real SOC platforms** — Splunk SOAR, Cortex XSOAR, and Microsoft Sentinel all use similar structured response steps.

---

### 🔗 References

- MITRE ATT&CK T1566.002: https://attack.mitre.org/techniques/T1566/002/
- VirusTotal URL report: https://www.virustotal.com/gui/url/...
- VirusTotal IP report: https://www.virustotal.com/gui/ip-address/146.56.195.192

---
