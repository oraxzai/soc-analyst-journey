<div align="center">

# 📧 Analysis Report — Project 02

### Phishing Email Forensics & Threat Intelligence

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=600&size=16&pause=1000&color=FF4444&center=true&vCenter=true&width=600&lines=Challenge+01%3A+PhishStrike;Challenge+02%3A+GrabThePhisher;Challenge+03%3A+IcedID" alt="Typing SVG" />

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

## 🎣 Challenge 02 — GrabThePhisher

<div align="center">

[![Platform](https://img.shields.io/badge/Platform-CyberDefenders-blue?style=for-the-badge&logo=cyberdefenders&logoColor=white)](#)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)](#)
[![Score](https://img.shields.io/badge/Score-10%2F10-brightgreen?style=for-the-badge)](#)
[![Date](https://img.shields.io/badge/Date-Oct%2013%2C%202026-lightgrey?style=for-the-badge)](#)

</div>

---

### 📋 Scenario Overview

The challenge provided a phishing kit designed to impersonate the **MetaMask** cryptocurrency wallet. The goal was to analyze the backend code of the kit to understand how credentials (seed phrases) were being stolen and where they were being sent.

---

### 🔍 Investigation

#### 🧬 IOC Extraction

<div align="center">

| 🚨 IOC | 🏷️ Type | 💬 Reputation / Notes |
|:-------|:--------|:---------------------|
| `metamask.php` | File | The backend PHP script executing the phishing logic |
| `api.sypexgeo.net` | URL | Geolocation API used to retrieve victim's country/city |
| `5442785564` | Telegram ID | The Chat ID of the attacker's channel |
| `5457463144:AAG8t4k7e2ew3tTi0IBShcWbSia0Irvxm10` | Token | Telegram Bot API Token used for exfiltration |
| `j1j1b1s@m3r0` | Alias | Attacker's signature/alias found in the code comments |

</div>

#### 🦠 Malware Analysis

- **Language:** The kit is written in **PHP**.
- **Exfiltration Medium:** The attacker uses **Telegram** to dump stolen credentials. The script hardcodes the bot token and chat ID to send the data in real-time.
- **Victim Data Collection:** The script uses the **Sypex Geo** API to retrieve the victim's geolocation based on their IP address, and also logs their User-Agent.
- **Stolen Data:** The `log.txt` file in the kit showed **3** previously collected seed phrases, confirming the kit had already been active and successful.

---

### 🎯 Verdict

<div align="center">

| 🛑 Classification | ⚡ Severity | 🎯 Confidence |
|:-----------------|:-----------:|:-------------:|
| **True Positive** (Active Phishing Kit) | 🔴 **High** | 🟢 **High** |

</div>

**Reasoning:**
The analysis confirmed an active, functional phishing kit targeting MetaMask users. The kit successfully exfiltrates seed phrases via a Telegram bot, uses a geolocation service for victim profiling, and had already captured credentials from 3 victims. The attacker's alias (`j1j1b1s@m3r0`) and the specific bot credentials were identified and extracted.

---

### 🧭 MITRE ATT&CK Mapping

<div align="center">

| ⚔️ Tactic | 🛠️ Technique | 🆔 ID |
|:----------|:-------------|:-----:|
| Initial Access | Phishing | `T1566` |
| Credential Access | Credentials from Web Browsers | `T1555.003` |
| Exfiltration | Exfiltration Over Web Service (Telegram) | `T1567.002` |

</div>

---

### 💡 Lessons Learned

1.  **Source Code Analysis:** Attackers often leave their Telegram tokens and chat IDs in plaintext in the source code of their phishing kits. Always read the PHP/JS files.
2.  **Legitimate Service Abuse:** Telegram is frequently abused by attackers to exfiltrate data because it is encrypted and often bypasses traditional firewall rules.
3.  **File Artifacts:** The `log.txt` file is a goldmine for threat intelligence, showing exactly how many victims were compromised before the kit was taken down.

---

## 🎣 Challenge 03 — IcedID

<div align="center">

[![Platform](https://img.shields.io/badge/Platform-CyberDefenders-blue?style=for-the-badge&logo=cyberdefenders&logoColor=white)](#)
[![Difficulty](https://img.shields.io/badge/Difficulty-Easy-brightgreen?style=for-the-badge)](#)
[![Score](https://img.shields.io/badge/Score-6%2F6-brightgreen?style=for-the-badge)](#)
[![Date](https://img.shields.io/badge/Date-Oct%2013%2C%202026-lightgrey?style=for-the-badge)](#)

</div>

---

### 📋 Scenario Overview

A threat intelligence exercise focused on a specific IcedID malware sample. The goal was to identify the malware's execution mechanisms, associated threat actor, and the infrastructure used for payload delivery.

---

### 🔍 Investigation

#### 🧬 IOC Extraction

<div align="center">

| 🚨 IOC | 🏷️ Type | 💬 Reputation / Notes |
|:-------|:--------|:---------------------|
| `document-1982481273.xlsm` | File | The malicious Excel macro document |
| `3003.gif` | File | The payload disguised as a GIF image |
| `tajushariya.com` | Domain | One of the domains hosting the malicious GIF |
| `NameCheap` | Registrar | The DNS registrar predominantly used for the hosting domains |
| `GOLD CABIN` | Threat Actor | The threat group associated with this IcedID campaign |
| `URLDownloadToFileA` | Function | The Windows API function used to download the payload |

</div>

#### 🦠 Malware Analysis

- **Execution Mechanism:** The malware uses the **`URLDownloadToFileA`** API function to download the extra payload (the `.gif` file) from multiple domains onto the system.
- **Delivery:** The payload was hosted across **5** different domains (e.g., `metaflip.io`, `tajushariya.com`, `agenbolatermurah.com`) to ensure redundancy in case one was taken down.
- **Threat Actor Attribution:** The threat actor linked to this IcedID sample is **GOLD CABIN** (also known as TA551), a known cybercriminal group that distributes malware via phishing campaigns.

---

### 🎯 Verdict

<div align="center">

| 🛑 Classification | ⚡ Severity | 🎯 Confidence |
|:-----------------|:-----------:|:-------------:|
| **True Positive** (IcedID Malware Distribution) | 🔴 **High** | 🟢 **High** |

</div>

**Reasoning:**
The analysis confirmed that the malicious `.xlsm` document uses the `URLDownloadToFileA` API to fetch malicious `.gif` files from a network of 5 domains. The infrastructure is linked to the GOLD CABIN threat actor, and the domains are predominantly registered through NameCheap. This demonstrates a clear, well-orchestrated malware delivery chain.

---

### 🧭 MITRE ATT&CK Mapping

<div align="center">

| ⚔️ Tactic | 🛠️ Technique | 🆔 ID |
|:----------|:-------------|:-----:|
| Initial Access | Phishing: Spearphishing Attachment | `T1566.001` |
| Execution | User Execution: Malicious File | `T1204.002` |
| Command & Control | Application Layer Protocol (Web) | `T1071.001` |

</div>

---

### 💡 Lessons Learned

1.  **Threat Actor Attribution:** Linking a malware sample to a specific threat actor (GOLD CABIN) helps in understanding the adversary's tactics, techniques, and procedures (TTPs).
2.  **Domain Infrastructure:** Malware authors register multiple domains with the same registrar (NameCheap) as a backup. Identifying the registrar helps in taking down the infrastructure.
3.  **Windows API Abuse:** The `URLDownloadToFileA` function is frequently abused by downloaders to fetch additional payloads silently.

---

<div align="center">

### ⚡ *"Every email tells a story. Every header is a clue."* ⚡

</div>
