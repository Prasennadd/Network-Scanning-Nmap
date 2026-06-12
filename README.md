# 🔍 Network Scanning & Enumeration Lab — Nmap

![Security](https://img.shields.io/badge/Security-Network%20Scanning-red)
![Tools](https://img.shields.io/badge/Tools-Nmap%20%7C%20Kali%20Linux-blue)
![Status](https://img.shields.io/badge/Status-Completed-green)
![Environment](https://img.shields.io/badge/Environment-Home%20Network%20Lab-yellow)

> ⚠️ **Disclaimer:** All scanning was performed on a **personally owned home network and devices** for **educational purposes only**. Scanning networks or systems you do not own is illegal. This research is intended solely to understand network enumeration techniques and improve security awareness.

---

## 📌 Table of Contents
- [Objective](#-objective)
- [Lab Environment Setup](#-lab-environment-setup)
- [Tools Used](#-tools-used)
- [What is Nmap?](#-what-is-nmap)
- [Methodology](#-methodology)
- [Key Findings](#-key-findings)
- [Scan Walkthrough](#-scan-walkthrough)
- [Scan Results Summary](#-scan-results-summary)
- [Mitigation Recommendations](#-mitigation-recommendations)
- [Lessons Learned](#-lessons-learned)
- [References](#-references)

---

## 🎯 Objective

The goal of this project was to perform **network scanning and enumeration** on a personally owned home network using Nmap. The focus areas were:

- Discovering all **active hosts** on the network
- Identifying **open ports** and running services
- Detecting **service versions** to assess potential vulnerabilities
- Performing **OS detection** on connected devices
- Documenting findings to understand the network's attack surface

---

## 🖥️ Lab Environment Setup

| Component | Details |
|---|---|
| **Scanner Machine** | Kali Linux (VM / Live Boot) |
| **Target Network** | Personally owned home network |
| **Target Devices** | Router, personal PC, mobile devices |
| **Network Range** | 192.168.1.0/24 (typical home subnet) |
| **Permission** | Personally owned network — full authorization |

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| **Nmap** | Network scanning, port scanning, OS & service detection |
| **Kali Linux** | Scanning environment |

---

## 🔎 What is Nmap?

**Nmap (Network Mapper)** is an open-source tool used for network discovery and security auditing. It works by sending crafted packets to target hosts and analyzing responses to determine:

```
What devices are online?      → Host Discovery
What ports are open?          → Port Scanning
What services are running?    → Service Detection
What OS is the device using?  → OS Detection
What version is the service?  → Version Detection
```

Nmap is used by security professionals worldwide for **network inventory, vulnerability assessment, and penetration testing.**

---

## 📋 Methodology

```
1. Identify Network Range
        ↓
2. Host Discovery (Ping Scan)
        ↓
3. Port Scanning (All Ports)
        ↓
4. Service & Version Detection
        ↓
5. OS Detection
        ↓
6. Aggressive Scan (Combined)
        ↓
7. Analysis & Documentation
```

### Phase 1 — Identify Network Range
- Identified home network subnet using `ifconfig` / `ip a`
- Confirmed network range (e.g., 192.168.1.0/24)

### Phase 2 — Host Discovery
- Used **ping scan** to find all active devices on the network
- Mapped out which IP addresses had live hosts

### Phase 3 — Port Scanning
- Scanned all 65535 ports on target devices
- Identified open, closed, and filtered ports

### Phase 4 — Service & Version Detection
- Used Nmap's `-sV` flag to detect running services and their versions
- Identified potentially outdated or vulnerable service versions

### Phase 5 — OS Detection
- Used Nmap's `-O` flag to fingerprint operating systems
- Identified OS type and version for each active host

### Phase 6 — Aggressive Scan
- Combined OS detection, version detection, script scanning, and traceroute
- Generated a comprehensive profile of each device

---

## 🔍 Key Findings

### Finding 1 — Multiple Devices Discovered on Network
- Home network had several active hosts (router, PC, mobile, IoT devices)
- Each device exposed a different set of open ports and services
- IoT devices often had **more open ports than expected**

### Finding 2 — Router Had Several Open Ports
- Common open ports found: 80 (HTTP), 443 (HTTPS), 53 (DNS), 22 (SSH)
- Admin panel accessible over HTTP — **unencrypted admin access is a risk**
- Default router services running on standard ports make them easy to fingerprint

### Finding 3 — Outdated Service Versions Detected
- Service version detection revealed some devices running older software versions
- Older versions may have **known CVEs (vulnerabilities)**
- Regular firmware/software updates are critical

### Finding 4 — OS Fingerprinting Was Highly Accurate
- Nmap correctly identified OS types for most devices
- This information can be used by attackers to **target OS-specific exploits**
- Hiding OS information requires firewall rules or packet filtering

---

## 🚀 Scan Walkthrough

### Step 1 — Find Your Network Range
```bash
ip a
# or
ifconfig
# Look for inet address e.g. 192.168.1.5
# Your network range: 192.168.1.0/24
```

### Step 2 — Host Discovery (Ping Scan)
```bash
sudo nmap -sn 192.168.1.0/24
# -sn : ping scan, no port scan
# Shows all active hosts on the network
```

### Step 3 — Basic Port Scan
```bash
sudo nmap 192.168.1.1
# Scans top 1000 common ports on target
# Shows open/closed/filtered ports
```

### Step 4 — Full Port Scan (All 65535 Ports)
```bash
sudo nmap -p- 192.168.1.1
# -p- : scan all 65535 ports
# More thorough but takes longer
```

### Step 5 — Service & Version Detection
```bash
sudo nmap -sV 192.168.1.1
# -sV : detect service versions
# Shows what software is running on each port
```

### Step 6 — OS Detection
```bash
sudo nmap -O 192.168.1.1
# -O : OS fingerprinting
# Identifies the operating system of the target
```

### Step 7 — Aggressive Scan (All Combined)
```bash
sudo nmap -A 192.168.1.1
# -A : aggressive scan
# Combines OS detection + version detection
#      + script scanning + traceroute
```

### Step 8 — Save Scan Results
```bash
sudo nmap -A 192.168.1.0/24 -oN scan_results.txt
# -oN : save output to a text file
```

---

## 📊 Scan Results Summary

| Device | Open Ports | Services Detected | OS Detected |
|---|---|---|---|
| **Router** | 80, 443, 53, 22 | HTTP, HTTPS, DNS, SSH | Linux (embedded) |
| **Personal PC** | 135, 445 | SMB, RPC | Windows 10 |
| **Mobile Device** | None visible | — | Android |
| **IoT Device** | 80, 23 | HTTP, Telnet | Embedded Linux |

> ⚠️ Telnet (port 23) on an IoT device is a serious risk — Telnet transmits data in **plaintext**.

---

## 🛡️ Mitigation Recommendations

| Vulnerability | Recommendation |
|---|---|
| Open Telnet (port 23) | Disable Telnet — use SSH instead |
| HTTP admin panel on router | Enable HTTPS for router admin access |
| Outdated service versions | Keep router firmware & devices updated |
| SSH open on router | Disable if unused; use key-based auth if needed |
| OS fingerprinting possible | Enable firewall rules to limit OS info leakage |
| SMB open (port 445) | Restrict SMB access; disable if unused |

---

## 📚 Lessons Learned

- Nmap is one of the most powerful tools for understanding a network's **attack surface**
- Even a simple home network exposes more information than most people realize
- **IoT devices** are often the weakest link — old firmware, open Telnet ports
- Service version detection is critical — old versions = known CVEs = easy targets
- This knowledge directly connects to **network security and endpoint hardening**
- Always scan your own network periodically to stay aware of your exposure

---

## 📖 References

- [Nmap Official Documentation](https://nmap.org/docs.html)
- [Nmap Cheat Sheet](https://www.stationx.net/nmap-cheat-sheet/)
- [MITRE ATT&CK — Network Scanning](https://attack.mitre.org/techniques/T1046/)
- [CVE Database](https://cve.mitre.org/)
- [Kali Linux Official Site](https://www.kali.org/)

---

## 🔗 Related Projects
- 🖥️ [Endpoint Vulnerability Analysis](https://github.com/Prasennadd/Endpoint-Vulnerability-Analysis)
- 📡 [WiFi Security & Hash Cracking](https://github.com/Prasennadd/WiFi-Security-And-Hash-Cracking)

---

## 👨‍💻 Author

**Manova Prasenna Raj D**
ECE Student | Sathyabama Institute of Science and Technology, Chennai
🔗 [LinkedIn](https://linkedin.com/in/prasenna-raj/) | 🐙 [GitHub](https://github.com/Prasennadd) | 📧 prasennadraj@gmail.com

---

*This project was conducted solely for educational and research purposes on a personally owned home network.*
