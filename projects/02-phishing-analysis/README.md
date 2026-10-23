<div align="center">

# 📧 Project 02 — Phishing Email Analysis

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=18&pause=1000&color=36BCF7&center=true&vCenter=true&width=600&lines=Platform%3A+CyberDefenders;Deep-dive+into+Email+Forensics;Phishing+Kit+Analysis+%26+IOC+Extraction" alt="Typing SVG" />

[![Status](https://img.shields.io/badge/Status-IN%20PROGRESS-yellow?style=for-the-badge&logo=statuspage&logoColor=white)](#)
[![Platform](https://img.shields.io/badge/Platform-CyberDefenders-blue?style=for-the-badge&logo=cyberdefenders&logoColor=white)](#)
[![Started](https://img.shields.io/badge/Started-Oct%2013%2C%202026-lightgrey?style=for-the-badge&logo=calendar&logoColor=white)](#)

</div>

---

## 🎯 Mission Objective

Deep-dive investigation of phishing emails focusing on:
- 📨 **Email Header Analysis** (SPF, DKIM, DMARC)
- 🔍 **IOC Extraction** (URLs, IPs, Hashes, Domains)
- 🕵️ **Phishing Kit Identification** & Analysis
- 📝 **Formal Forensic Reporting**

---

## 🏆 Challenge Progress

<div align="center">

| # | 🎣 Challenge | ⚡ Difficulty | 📊 Status | 📄 Report |
|:-:|-------------|:-------------:|:---------:|:---------:|
| 01 | **PhishStrike** | 🟡 Medium | ✅ **COMPLETE** | [View Report](./analysis-report.md#challenge-01--phishstrike) |
| 02 | *GrabThePhisher* | 🟡 Medium | ⏳ Pending | — |
| 03 | *IcedID* | 🟡 Medium | ⏳ Pending | — |

**Progress:** `██░░░░░░░░` **1 / 3 Challenges (33%)**

</div>

---

## 🧠 Skills Demonstrated

<div align="center">

| 🛡️ Investigation | 🧰 Tools Used | 🧭 MITRE ATT&CK |
|:---:|:---:|:---:|
| Email Header Analysis | VirusTotal, MXToolbox | T1566.002 |
| Malware Identification | URLHaus, MalwareBazaar | T1204.001 |
| Registry Persistence | ANY.RUN, Triage | T1547.001 |
| C2 Infrastructure | VirusTotal, Triage | T1071.001 |
| Exfiltration Channels | Triage (GET Requests) | T1567.002 |

</div>

---

## 📸 Evidence Vault

All investigation screenshots are securely stored in the [`./evidence/`](./evidence/) folder.

<div align="center">

| Challenge | Screenshots | Status |
|:---------:|:-----------:|:------:|
| PhishStrike | 6 Evidence Files | ✅ Uploaded |

</div>

---

## 🎯 IOC Database

A machine-readable list of all extracted indicators is maintained in [`ioc-list.csv`](./ioc-list.csv).

<div align="center">

| 🎯 Challenge | 🚨 Threat | 🧬 Malware Family | 🌐 C2 Domain |
|:------------:|:---------:|:-----------------:|:------------:|
| PhishStrike | Invoice Phishing | BitRAT / CoinMiner / AsyncRAT | `gh9st.mywire.org` |

</div>

---

## 📁 Related Files

- 📄 [`analysis-report.md`](./analysis-report.md) — Detailed forensic writeups
- 📊 [`ioc-list.csv`](./ioc-list.csv) — Machine-readable IOC database
- 📸 [`evidence/`](./evidence/) — Investigation screenshots

---

<div align="center">

### ⚡ *"Every email tells a story. Every header is a clue."* ⚡

</div>
