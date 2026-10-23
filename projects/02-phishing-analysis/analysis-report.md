<div align="center">

# 📧 Analysis Report — Project 02

### Phishing Email Forensics & Threat Intelligence

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=16&pause=1000&color=FF4444&center=true&vCenter=true&width=600&lines=Challenge+01%3A+PhishStrike;Email+Headers+%7C+Malware+Analysis+%7C+C2+Infrastructure" alt="Typing SVG" />

</div>

---

## 🎣 Challenge 01 — PhishStrike

<div align="center">

[![Platform](https://img.shields.io/badge/Platform-CyberDefenders-blue?style=for-the-badge&logo=cyberdefenders&logoColor=white)](#)
[![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange?style=for-the-badge)](#)
[![Score](https://img.shields.io/badge/Score-11%2F11-brightgreen?style=for-the-badge)](#)
[![Date](https://img.shields.io/badge/Date-Oct%2013%2C%202026-lightgrey?style=for-the-badge)](#)

</div>

---

### 📋 Email Overview

<div align="center">

| 📨 Field | 📝 Value |
|:---------|:---------|
| **From** | `ERIKA JOHANA LOPEZ VALIENTE <erikajohana.lopez@uptc.edu.co>` |
| **To** | `undisclosed-recipients:;` *(Target: servicios.informaticos@fsfb.org.co)* |
| **Subject** | `COMMERCIAL PURCHASE RECEIPT ONLINE 27 NOV` |
| **Date** | `Thu, 9 Dec 2022 09:58:26 +0100` |
| **Sender IP** | `18.208.22.104` (AWS) |
| **Return-Path** | `erikajohana.lopez@uptc.edu.co` |

</div>

---

### 🔍 Investigation

#### 🛡️ Header Analysis

> **SPF:** `softfail` (Sender IP `18.208.22.104` is not authorized to send for `uptc.edu.co`)
> **DKIM:** `fail` (no key for signature)
> **DMARC:** `none`
> **Received Chain:** The email originated from an AWS IP (`18.208.22.104`) and passed through Trend Micro Email Security before reaching the target.

#### 🧬 IOC Extraction

<div align="center">

| 🚨 IOC | 🏷️ Type | 💬 Reputation / Notes |
|:-------|:--------|:---------------------|
| `18.208.22.104` | IP | Spoofed sender IP (AWS) |
| `107.175.247.199` | IP | Hosting malicious payloads |
| `http://107.175.247.199/loader/install.exe` | URL | Initial malware downloader |
| `http://ripley.studio/loader/uploads/Qanjttrbv.jpeg` | URL | C2/Communication URL |
| `bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539` | SHA256 | BitRAT Payload |
| `453fb1c4b3b48361fa8a67dcedf1eaec39449cb5a146a7770c63d1dc0d7562f0` | SHA256 | CoinMiner Payload |
| `Jzwvix.exe` | File | BitRAT persistence executable |
| `gh9st.mywire.org` | Domain | BitRAT C2 Domain |
| `bot5610920260` | Telegram ID | AsyncRAT Exfiltration Channel |

</div>

#### 🦠 Malware Analysis

- **URLHaus:** The hosting IP (`107.175.247.199`) was associated with multiple malware families, including **CoinMiner**, **BitRAT**, and **AsyncRAT**.
- **BitRAT Persistence:** The BitRAT sample drops `Jzwvix.exe` and adds it to the `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run` registry key.
- **PowerShell Delay:** The BitRAT loader uses a Base64 encoded PowerShell command with a **50-second delay** to evade sandbox detection.
- **AsyncRAT Exfiltration:** The AsyncRAT sample uses a Telegram Bot (`bot5610920260`) to exfiltrate data via `api.telegram.org`.

---

### 🎯 Verdict

<div align="center">

| 🛑 Classification | ⚡ Severity | 🎯 Confidence |
|:-----------------:|:-----------:|:-------------:|
| **True Positive** (Phishing / Malware Distribution) | 🔴 **High** | 🟢 **High** |

</div>

**Reasoning:**
The email used a spoofed sender address and failed SPF/DKIM authentication. It contained a link disguised as an invoice that pointed to a malicious executable hosted on an IP address. Threat intelligence revealed that this IP hosted multiple malware families (CoinMiner, BitRAT, AsyncRAT). The BitRAT payload established persistence via the registry autorun key and attempted to communicate with a C2 domain (`gh9st.mywire.org`), while the AsyncRAT payload used Telegram for data exfiltration.

---

### 🧭 MITRE ATT&CK Mapping

<div align="center">

| ⚔️ Tactic | 🛠️ Technique | 🆔 ID |
|:----------|:-------------|:-----:|
| Initial Access | Phishing: Spearphishing Link | `T1566.002` |
| Execution | User Execution: Malicious Link | `T1204.001` |
| Execution | PowerShell | `T1059.001` |
| Persistence | Registry Run Keys / Startup Folder | `T1547.001` |
| Command & Control | Application Layer Protocol (Web) | `T1071.001` |
| Exfiltration | Exfiltration Over Web Service | `T1567.002` |

</div>

---

### 💡 Lessons Learned

1.  **Email Header Analysis:** SPF, DKIM, and DMARC failures are immediate red flags for spoofed emails.
2.  **IOC Pivoting:** A single malicious IP can host multiple different malware families. Always pivot from the IP to URLHaus/MalwareBazaar to find the full scope.
3.  **Sandbox Analysis:** For persistent malware, checking the Behavior tab (VirusTotal) or Process Tree (ANY.RUN) is critical to find the exact registry keys and dropped files.
4.  **Exfiltration Channels:** Attackers abuse legitimate services like Telegram to hide their traffic and bypass firewalls.

---

<div align="center">

### ⚡ *"Every email tells a story. Every header is a clue."* ⚡

</div>
