<div align="center">

# 🛡️ PROJECT 01 — LIVE SOC ALERT MONITORING

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=22&pause=1000&color=36BCF7&center=true&vCenter=true&width=700&lines=%F0%9F%9A%A8+10+Alerts+Triaged;%F0%9F%94%B4+5+Compromises+Confirmed;%F0%9F%9B%A1%EF%B8%8F+2+Ransomware+Attacks+Contained;%F0%9F%A7%AC+1+Supply+Chain+Backdoor+Exposed" alt="Typing SVG" />

[![Status](https://img.shields.io/badge/Status-COMPLETE-brightgreen?style=for-the-badge&logo=checkmarx&logoColor=white)](#)
[![Alerts](https://img.shields.io/badge/Alerts-10%2F10-red?style=for-the-badge&logo=letsdefend&logoColor=white)](#)
[![Platform](https://img.shields.io/badge/Platform-LetsDefend-00B4D8?style=for-the-badge&logo=letsdefend&logoColor=white)](#)
[![Duration](https://img.shields.io/badge/Duration-10%20Days-blue?style=for-the-badge&logo=clockify&logoColor=white)](#)

**Started:** October 1, 2026 | **Completed:** October 10, 2026

</div>

---

## 🎯 MISSION OBJECTIVE

<div align="center">

**Triage 10 live SOC alerts. Document every investigation. Build a real L1 portfolio.**

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=500&size=16&pause=2000&color=00FF00&center=true&vCenter=true&width=700&lines=Alert+Triage+%E2%86%92+Evidence+Correlation+%E2%86%92+Verdict+%E2%86%92+Documentation;Build+the+analyst+mindset" alt="Typing SVG" />

</div>

---

## 🏆 PROJECT STATUS

<div align="center">

| 📊 Alerts Triaged | 🔴 Confirmed Compromises | 🛡️ Attacks Blocked | 📸 Screenshots | ⚡ MITRE Techniques |
|:-----------------:|:------------------------:|:------------------:|:--------------:|:-------------------:|
| **10 / 10** | **5** | **2** | **80+** | **45+** |

**Progress:** `████████████████████` **100%** ✅

</div>

---

## 📋 ALERT PROGRESS

<div align="center">

| # | 🎯 Alert | ⚡ Severity | ✅ Verdict | 📅 Date | 📄 Log |
|:-:|---------|:-----------:|:---------:|:-------:|:------:|
| 1 | SOC101 - Phishing Mail Detected (87) | 🟡 Medium | ✅ True Positive | Oct 2 | [log](#) |
| 2 | SOC205 - Malicious Macro Executed (231) | 🟠 High | ✅ True Positive | Oct 3 | [log](#) |
| 3 | SOC325 - Unauthorized Cloud Access (303) | 🟢 Low | ✅ True Positive | Oct 4 | [log](#) |
| 4 | SOC276 - Account Discovery Attempt (251) | 🔴 **Critical** | ✅ True Positive | Oct 6 | [log](#) |
| 5 | SOC312 - Template Modification (290) | 🔴 **Critical** | ✅ True Positive | Oct 7 | [log](#) |
| 6 | SOC293 - Exfiltration Over Pastebin (269) | 🔴 **Critical** | ✅ True Positive | Oct 7 | [log](#) |
| 7 | SOC317 - VM Detection Attempt (295) | 🔴 **Critical** | ✅ True Positive | Oct 9 | [log](#) |
| 8 | SOC328 - Akira Ransomware (306) | 🟠 High | ✅ True Positive | Oct 9 | [log](#) |
| 9 | SOC194 - Reverse Shell Detected (144) | 🟠 High | ✅ True Positive | Oct 10 | [log](#) |
| 10 | SOC271 - XZ Backdoor CVE-2024-3094 (247) | 🔴 **Critical** | ✅ True Positive | Oct 10 | [log](#) |

</div>

---

## 🎯 ATTACK TYPES MASTERED

<div align="center">

| 🎣 Phishing | 🦠 Malware | ☁️ Cloud Attack | 🚨 Account Recon |
|:-----------:|:----------:|:---------------:|:----------------:|
| ✅ 3 alerts | ✅ 3 alerts | ✅ 1 alert | ✅ 1 alert |

| 📄 Persistence | 📤 Exfiltration | 🕵️ VM Evasion | 🐚 Reverse Shell |
|:--------------:|:---------------:|:-------------:|:----------------:|
| ✅ 2 alerts | ✅ 1 alert | ✅ 1 alert | ✅ 1 alert |

| 🧬 Supply Chain |
|:---------------:|
| ✅ 1 alert |

</div>

---

## ⚡ HIGHLIGHT INVESTIGATIONS

### 🔥 THE FIRST COMPROMISE — Alert #4

> **Attacker IP:** `185.107.80.128` (79 AbuseIPDB reports, NForce VPN)
> **Target:** VirtuLinux (172.16.17.186)
> **Finding:** 3 successful SSH logins → account discovery via `getent passwd`

**Breakthrough:** Basic view showed 0 events. **Pro view** revealed the smoking gun — 3 "Accepted password" log entries.

---

### 💰 RANSOMWARE CONTAINED — Alert #8

> **Threat:** Akira Ransomware (60/71 VT malicious)
> **Target:** Vergil (172.16.17.130)
> **Timeline:** 62 seconds from execution to ransom note

**Breakthrough:** `.akira` extension search → 0 events. **Encryption prevented.** Attack contained before impact.

---

### 🧬 SUPPLY CHAIN ATTACK — Alert #10

> **Threat:** XZ Backdoor (CVE-2024-3094) — 37/63 VT malicious
> **Target:** SSHDevServer01 (172.16.17.121)
> **Finding:** Backdoored `liblzma.so.5.6.1` in `/usr/local/lib/`

**Breakthrough:** Confirmed live vulnerable version (xz 5.6.1) + located malicious file shadowing the legitimate 5.2.4 library.

---

## 🧭 MITRE ATT&CK COVERAGE

<div align="center">

| **Initial Access** | **Execution** | **Persistence** |
|:------------------:|:-------------:|:---------------:|
| T1566.001, T1566.002, T1133, T1195 | T1059.001, T1059.003, T1059.004, T1059.005, T1047, T1106, T1204.002 | T1137.001, T1547.009 |

| **Defense Evasion** | **Credential Access** | **Discovery** |
|:-------------------:|:---------------------:|:-------------:|
| T1078, T1535, T1221, T1497.001 | T1110 | T1087, T1033, T1082, T1057 |

| **Command & Control** | **Exfiltration** | **Impact** |
|:---------------------:|:----------------:|:----------:|
| T1071, T1071.001, T1105, T1571 | T1567.002 | T1485, T1486, T1490 |

</div>

---

## 🎯 IOCs EXTRACTED

<div align="center">

| 🎣 Phishing | 🦠 Malware | 🚨 Attacker IPs |
|:-----------:|:----------:|:---------------:|
| `huangaybantiep.xyz` | `downloader.logan/w97m` | `185.107.80.128` |
| `lethuyan852@gmail.com` | `greyhathacker.net` | `181.214.131.108` |
| `dachfix.com` | `heg.com` | `134.209.145.73` |
| `mate@instagram.com.tr` | `system_users.ps1` | `37.19.205.203` |
| `lnstagrams.com.tr` | `test.dotm` | `185.106.94.194` |
| `instagram.com.tr` | `liblzma.so.5.6.1` | `172.16.20.3` |

**Total IOCs:** **45+** documented across all 10 alerts

</div>

---

## 💡 KEY LESSONS LEARNED

<div align="center">

| # | 💡 Lesson | 🎯 Alert |
|:-:|-----------|:--------:|
| 1 | VirusTotal "0 detections" ≠ safe | #1 |
| 2 | **Search process names BEFORE IOCs** | #2 |
| 3 | "Device Action: Blocked" = contained | #3 |
| 4 | **Pro view > Basic view** | #4 |
| 5 | AbuseIPDB complements VirusTotal | #4 |
| 6 | "Accepted password" in logs = smoking gun | #4 |
| 7 | **Timing correlation proves causation** | #5, #7 |
| 8 | `Normal.dotm` = full persistence | #5 |
| 9 | RDP brute force is common in enterprise | #5, #7 |
| 10 | Phishing → exfil = full kill chain | #6 |
| 11 | Pastebin = common exfil destination | #6 |
| 12 | VM detection = sandbox evasion | #7 |
| 13 | Ransomware runs in seconds | #8 |
| 14 | "Detected" ≠ "Blocked" | #8 |
| 15 | Typosquatting: `lnstagrams` vs `instagram` | #9 |
| 16 | "Backdoor exploited" ≠ "connection succeeded" | #9 |
| 17 | **"Installed" ≠ "Executed"** | #10 |
| 18 | `/usr/local/lib/` takes precedence over `/usr/lib/` | #10 |

</div>

---

## 📸 EVIDENCE

<div align="center">

**Every alert documented with screenshots, logs, IOCs, and verdicts.**

| Alert | 📸 Screenshots | 🎯 Status |
|:-----:|:--------------:|:---------:|
| #1 | 11 screenshots | ✅ Complete |
| #2 | 13 screenshots | ✅ Complete |
| #3 | 5 screenshots | ✅ Complete |
| #4 | 8 screenshots | ✅ Complete |
| #5 | 9 screenshots | ✅ Complete |
| #6 | 12 screenshots | ✅ Complete |
| #7 | 5 screenshots | ✅ Complete |
| #8 | 6 screenshots | ✅ Complete |
| #9 | 6 screenshots | ✅ Complete |
| #10 | 3 screenshots | ✅ Complete |

**Total: 80+ screenshots**

</div>

---

## 🛠️ TOOLS MASTERED

<div align="center">

![VirusTotal](https://img.shields.io/badge/VirusTotal-394EFF?style=for-the-badge&logo=virustotal&logoColor=white)
![AbuseIPDB](https://img.shields.io/badge/AbuseIPDB-CC0000?style=for-the-badge&logo=abuseipdb&logoColor=white)
![LetsDefend](https://img.shields.io/badge/LetsDefend-00B4D8?style=for-the-badge&logo=letsdefend&logoColor=white)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE_ATT%26CK-FF0000?style=for-the-badge&logo=mitre&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)

**+ Pro View Log Analysis, Email Security, Endpoint Security, Proxy Logs, Firewall Logs**

</div>

---

## 🎓 SKILLS DEMONSTRATED

<div align="center">

✅ Alert Triage Workflow
✅ Threat Intelligence Correlation
✅ Log Forensics (Basic + Pro view)
✅ MITRE ATT&CK Mapping
✅ Incident Response Playbook Execution
✅ IOC Extraction & Classification
✅ Host Containment
✅ Supply Chain Attack Analysis
✅ Ransomware Investigation
✅ Reverse Shell Detection
✅ Evidence Documentation

</div>

---

## 🏆 PROJECT COMPLETION

<div align="center">

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=700&size=24&pause=1000&color=00FF00&center=true&vCenter=true&width=700&lines=%E2%9C%85+PROJECT+01+COMPLETE;%F0%9F%8F%86+10%2F10+Alerts+Triaged;%F0%9F%9A%80+Moving+to+Project+02" alt="Typing SVG" />

### 🎯 Next Up: **Project 02 — Phishing Email Analysis**

**Platform:** CyberDefenders

</div>

---

<div align="center">

### ⚡ *"Detection is not a tool — it's a mindset."* ⚡

**⭐ Star this repo if you're following the same journey ⭐**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/haris456/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/oraxzai)

</div>

---

*Project 01 Complete — October 10, 2026*
