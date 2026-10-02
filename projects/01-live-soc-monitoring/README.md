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
| MITRE ATT&CK | Threat technique mapping |
| Log Management (LetsDefend) | Internal log correlation |

---

## 📋 Alert Progress

| # | Alert | Severity | Verdict | Date | Log |
|---|-------|----------|---------|------|-----|
| 1 | SOC101 - Phishing Mail Detected (EventID 87) | Medium | ✅ True Positive | 2026-10-02 | [log](./triage-log.md#alert-1--soc101-phishing-mail-detected) |
| 2 | *(pending)* | | | | |
| 3 | *(pending)* | | | | |
| 4 | *(pending)* | | | | |
| 5 | *(pending)* | | | | |
| 6 | *(pending)* | | | | |
| 7 | *(pending)* | | | | |
| 8 | *(pending)* | | | | |
| 9 | *(pending)* | | | | |
| 10 | *(pending)* | | | | |

**Progress:** 1 / 10 alerts triaged (10%)

---

## 📊 Stats

| Metric | Value |
|--------|-------|
| Total Alerts Triaged | 1 |
| True Positives | 1 |
| False Positives | 0 |
| Benign | 0 |
| Average Time per Alert | ~2 hours (learning pace) |
| MITRE Techniques Mapped | 1 (T1566.002) |
| IOCs Extracted | 3 |

---

## 🧭 MITRE ATT&CK Techniques Covered

| Tactic | Technique | ID | Alert |
|--------|-----------|-----|-------|
| Initial Access | Phishing: Spearphishing Link | T1566.002 | #1 |

---

## 🎯 IOCs Extracted

| # | IOC | Type | Context | Alert |
|---|-----|------|---------|-------|
| 1 | `http://huangaybantiep.xyz` | URL | Phishing URL embedded in email body | #1 |
| 2 | `146.56.195.192` | IP | Sender SMTP IP — flagged malicious by Criminal IP | #1 |
| 3 | `lethuyan852@gmail.com` | E-mail Sender | Random-numbered Gmail phishing sender | #1 |

---

## 💡 Key Lessons Learned

1. **VirusTotal "0 detections" ≠ safe** — freshness of domains matters more than detection count
2. **Pyramid of evidence** — no single indicator is conclusive; the combined correlation is
3. **Log Management affects severity, not verdict** — no click lowers severity, but the email is still malicious
4. **IOC type classification matters** — URL Address vs IP Address vs E-mail Sender
5. **Playbooks mirror enterprise SOC tools** — Splunk SOAR, Cortex XSOAR, Microsoft Sentinel

---

## 📸 Screenshots

All investigation evidence is stored in [`./screenshots/`](./screenshots/) — named by alert ID and stage:

| Alert | Screenshots |
|-------|-------------|
| #1 | 11 screenshots documenting the full triage workflow |

---

## 📁 Related Files

- [`triage-log.md`](./triage-log.md) — Detailed writeups for every alert
- [`screenshots/`](./screenshots/) — Redacted investigation evidence
- [Root README](../../README.md) — Full portfolio overview

---

## 🎓 Skills Demonstrated in This Project

- Alert triage workflow (L1 SOC)
- Email phishing investigation
- Threat intelligence lookups (VirusTotal)
- Log correlation and analysis
- IOC extraction and classification
- Incident response playbook execution
- MITRE ATT&CK mapping
- Evidence documentation with screenshots
- Analytical writing (analyst notes, verdicts)

---

*Last updated: October 2, 2026*
