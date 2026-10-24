<div align="center">

# 📧 Project 02 — Phishing Email Analysis

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=18&pause=1000&color=36BCF7&center=true&vCenter=true&width=600&lines=Platform%3A+CyberDefenders;Deep-dive+into+Email+Forensics;Phishing+Kit+Analysis+%26+IOC+Extraction;PROJECT+COMPLETE" alt="Typing SVG" />

[![Status](https://img.shields.io/badge/Status-COMPLETE-brightgreen?style=for-the-badge&logo=statuspage&logoColor=white)](#)
[![Platform](https://img.shields.io/badge/Platform-CyberDefenders-blue?style=for-the-badge&logo=cyberdefenders&logoColor=white)](#)
[![Started](https://img.shields.io/badge/Started-Oct%2013%2C%202026-lightgrey?style=for-the-badge&logo=calendar&logoColor=white)](#)
[![Completed](https://img.shields.io/badge/Completed-Oct%2014%2C%202026-brightgreen?style=for-the-badge&logo=checkmarx&logoColor=white)](#)

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
| 02 | **GrabThePhisher** | 🟢 Easy | ✅ **COMPLETE** | [View Report](./analysis-report.md#challenge-02--grabthephisher) |
| 03 | **IcedID** | 🟢 Easy | ✅ **COMPLETE** | [View Report](./analysis-report.md#challenge-03--icedid) |

**Progress:** `██████████` **3 / 3 Challenges (100%)** 🎉

</div>

---

## 🧠 Skills Demonstrated

<div align="center">

| 🛡️ Investigation | 🧰 Tools Used | 🧭 MITRE ATT&CK |
|:---:|:---:|:---:|
| Email Header Analysis | VirusTotal, MXToolbox | T1566.002 |
| Malware Identification | URLHaus, MalwareBazaar | T1204.001 |
| PHP Source Code Analysis | TextEdit, Terminal | T1566 |
| Registry Persistence | ANY.RUN, Triage | T1547.001 |
| C2 Infrastructure | VirusTotal, Triage | T1071.001 |
| Exfiltration Channels | Triage, Telegram | T1567.002 |
| Threat Actor Attribution | MITRE ATT&CK, Malpedia | T1589 |
| Windows API Abuse | Triage, VirusTotal | T1105 |

</div>

---

## 📸 Evidence Vault

All investigation screenshots are securely stored in the [`./evidence/`](./evidence/) folder.

<div align="center">

| Challenge | Screenshots | Status |
|:---------:|:-----------:|:------:|
| PhishStrike | 6 Evidence Files | ✅ Uploaded |
| GrabThePhisher | 3 Evidence Files | ✅ Uploaded |
| IcedID | 5 Evidence Files | ✅ Uploaded |

</div>

---

## 🎯 IOC Database

A machine-readable list of all extracted indicators is maintained in [`ioc-list.csv`](./ioc-list.csv).

<div align="center">

| 🎯 Challenge | 🚨 Threat | 🧬 Malware Family | 🌐 C2 / Exfil |
|:------------:|:---------:|:-----------------:|:-------------:|
| PhishStrike | Invoice Phishing | BitRAT / CoinMiner / AsyncRAT | `gh9st.mywire.org` / Telegram |
| GrabThePhisher | Fake MetaMask Wallet | Phishing Kit | Telegram Bot API |
| IcedID | Malicious Excel Macro | IcedID | `tajushariya.com` |

</div>

---

## 📊 Project Statistics

<div align="center">

| 📈 Metric | 🎯 Value |
|:----------|:--------:|
| Challenges Completed | **3** |
| Total Questions Solved | **27** |
| IOCs Extracted | **25+** |
| Malware Families Identified | **5** (BitRAT, CoinMiner, AsyncRAT, IcedID, Phishing Kit) |
| MITRE Techniques Mapped | **12+** |
| Tools Used | **8+** (VirusTotal, URLHaus, MalwareBazaar, Triage, ANY.RUN, MITRE ATT&CK, WHOIS, TextEdit) |

</div>

---

## 📁 Related Files

- 📄 [`analysis-report.md`](./analysis-report.md) — Detailed forensic writeups
- 📊 [`ioc-list.csv`](./ioc-list.csv) — Machine-readable IOC database
- 📸 [`evidence/`](./evidence/) — Investigation screenshots

---

## 🎯 Key Takeaways

- **Phishing kits are often poorly secured:** Attackers leave Telegram tokens, chat IDs, and their aliases in plaintext.
- **Multi-stage malware requires pivoting:** A single email can lead to CoinMiner, BitRAT, and AsyncRAT.
- **Threat actor attribution matters:** Identifying GOLD CABIN (TA551) helps understand the adversary's TTPs.
- **Documentation is key:** Every IOC, hash, and domain must be logged for future defense.

---

<div align="center">

### ⚡ *"Every email tells a story. Every header is a clue."* ⚡

</div>
