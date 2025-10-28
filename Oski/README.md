# ⭐ Oski Lab - CyberDefenders Write-up ⭐

![CyberDefenders](https://img.shields.io/badge/CyberDefenders-Blue%20Team-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-green)
![Category](https://img.shields.io/badge/Category-Malware%20Analysis-red)
![Tools](https://img.shields.io/badge/Tools-VirusTotal%2C%20Any.run-orange)

## Lab Info

| Property | Value |
|----------|-------|
| **Platform** | CyberDefenders |
| **Category** | SOC Analyst Tier 1 - Level 1 |
| **Difficulty** | Easy |
| **Lab Type** | Malware Analysis |
| **Tags** | Blue Team, SIEM, Threat Intelligence, Sandbox Analysis |

---

## Overview

This writeup covers the analysis of the **Oski Lab** from CyberDefenders, focusing on malware analysis techniques used by SOC analysts. The lab demonstrates the investigation of a suspicious PowerPoint file that triggered a SIEM alert, requiring detailed forensic analysis to understand the threat.

**Key Learning Objectives:**
* Malware hash analysis using threat intelligence platforms
* Sandbox analysis with Any.run
* MITRE ATT&CK framework identification
* Command & Control (C2) server detection
* Malware behavior analysis and timeline reconstruction

---

## Scenario

> *The accountant at the company received an email titled "Urgent New Order" from a client late in the afternoon. When he attempted to access the attached invoice, he discovered it contained false order information. Subsequently, the SIEM solution generated an alert regarding downloading a potentially malicious file. Upon initial investigation, it was found that the PPT file might be responsible for this download. Could you please conduct a detailed examination of this file?*

**Initial Evidence:**
* **File Hash (MD5):** `12c1842c3ccafe7408c23ebf292ee3d9`
* **File Type:** PowerPoint presentation (.ppt)
* **SIEM Alert:** Malicious file download detected

---

## Analysis Methodology

### Phase 1: Hash-Based Threat Intelligence

**Tool Used:** VirusTotal
**Objective:** Identify malware family and basic characteristics

```bash
# Hash provided in evidence
MD5: 12c1842c3ccafe7408c23ebf292ee3d9
```

**VirusTotal Analysis Results:**
* **Detection Rate:** 62/73 antivirus engines flagged as malicious
* **Classification:** High-confidence malware detection
* **File Type:** Executable masquerading as PowerPoint

### Phase 2: Sandbox Analysis

**Tool Used:** Any.run Interactive Sandbox
**Objective:** Dynamic behavior analysis in controlled environment

**Sandbox Configuration:**
* **OS Environment:** Windows 7
* **Analysis Type:** Interactive malware execution
* **Duration:** Full behavior monitoring

---

## Questions & Solutions

### Q1: Malware Creation Time
**Question:** *Determining the creation time of the malware can provide insights into its origin. What was the time of malware creation?*

**Format:** `YYYY-MM-DD HH:MM`

**Solution Process:**
1. Navigate to VirusTotal Details tab
2. Locate "History" section
3. Identify compilation timestamp

**Answer:** `2022-09-28 17:40`

**Analysis:** The malware was compiled on September 28, 2022, at 5:40 PM, indicating recent threat development.

---

### Q2: Command & Control Server
**Question:** *Identifying the command and control (C2) server that the malware communicates with can help trace back to the attacker. Which C2 server does the malware in the PPT file communicate with?*

**Format:** `****://***.**.**.***/****************.***`

**Solution Process:**
1. In VirusTotal, navigate to "Relations" tab
2. Identify contacted URLs during execution
3. Cross-reference with Any.run network traffic

**Answer:** `http://185.56.76.179/sixteen_minutes.php`

**Analysis:** The C2 server uses a standard HTTP protocol with a PHP endpoint, typical of commodity malware families.

---

### Q3: First Library Request
**Question:** *Identifying the initial actions of the malware post-infection can provide insights into its primary objectives. What is the first library that the malware requests post-infection?*

**Format:** `*******.***`

**Solution Process:**
1. Examine VirusTotal "Behavior" section
2. Analyze imported libraries and DLL dependencies
3. Identify first library loaded during execution

**Answer:** `sqlite3.dll`

**Analysis:** SQLite3 library suggests data storage/retrieval capabilities, common in information-stealing malware.

---

### Q4: RC4 Encryption Key
**Question:** *By examining the provided Any.run report, what RC4 key is used by the malware to decrypt its base64-encoded string?*

**Format:** `****************************`

**Solution Process:**
1. Access Any.run analysis report
2. Click on malware executable (vpn.exe) in process tree
3. Select "CFG" (Configuration) option
4. Locate RC4 key in malware configuration

**Answer:** `sixteencharacterslengthkey`

**Analysis:** The 16-character RC4 key is used for encrypting/decrypting command communications with the C2 server.

---

### Q5: MITRE ATT&CK Technique
**Question:** *By examining the MITRE ATT&CK techniques displayed in the Any.run sandbox report, identify the main MITRE technique (not sub-techniques) the malware uses to steal the user's password.*

**Format:** `T****`

**Solution Process:**
1. In Any.run report, navigate to "MITRE ATT&CK" section
2. Identify techniques related to credential access
3. Focus on main technique codes (T-numbers)

**Answer:** `T1555`

**Analysis:** T1555 corresponds to "Credentials from Password Stores," indicating the malware targets stored authentication credentials.

---

### Q6: DLL Deletion Target Directory
**Question:** *By examining the child processes displayed in the Any.run sandbox report, which directory does the malware target for the deletion of all DLL files?*

**Format:** `*:\***********`

**Solution Process:**
1. Analyze child processes in Any.run report
2. Examine command line arguments for file operations
3. Identify directory path in deletion commands

**Answer:** `C:\ProgramData`

**Analysis:** The malware targets the ProgramData directory for DLL cleanup, likely to remove forensic evidence.

---

### Q7: Self-Deletion Timer
**Question:** *Understanding the malware's behavior post-data exfiltration can give insights into its evasion techniques. By analyzing the child processes, after successfully exfiltrating the user's data, how many seconds does it take for the malware to self-delete?*

**Solution Process:**
1. Examine command execution timeline in Any.run
2. Locate timeout commands in process tree
3. Identify delay before self-deletion

**Command Observed:**
```cmd
cmd.exe /c timeout /t 5 /nobreak & del /q /f C:\ProgramData\vpn.exe
```

**Answer:** `5`

**Analysis:** The malware implements a 5-second delay before self-deletion, allowing time for network communications to complete.

---

## Tools Used

| Category | Tools | Purpose |
|----------|-------|---------|
| **Threat Intelligence** | VirusTotal | Hash reputation, static analysis |
| **Sandbox Analysis** | Any.run | Dynamic behavior analysis |
| **IP Reputation** | AbuseIPDB | C2 server reputation check |
| **Framework** | MITRE ATT&CK | Technique classification |

---

## Attack Timeline

1. **Initial Infection** (T+0s): PowerPoint file executed
2. **Library Loading** (T+1s): SQLite3.dll imported for data operations
3. **C2 Communication** (T+2s): HTTP connection to 185.56.76.179
4. **Credential Harvesting** (T+3s): Password store access (T1555)
5. **Data Exfiltration** (T+4s): Stolen data transmitted to C2
6. **Evidence Cleanup** (T+5s): DLL files deleted from ProgramData
7. **Self-Deletion** (T+10s): Malware removes itself after 5-second delay

---

## Key Takeaways

### Technical Insights
* **Hash Analysis:** Essential first step for malware identification
* **Sandbox Analysis:** Provides detailed behavioral understanding
* **C2 Detection:** Network indicators crucial for threat hunting
* **MITRE Mapping:** Standardizes threat actor techniques

### SOC Analyst Skills Demonstrated
* **Threat Intelligence:** Leveraging multiple TI platforms
* **Dynamic Analysis:** Understanding malware behavior patterns  
* **Timeline Reconstruction:** Building attack narratives
* **Framework Application:** Using MITRE ATT&CK for classification

### Detection Opportunities
* **Network Signatures:** HTTP traffic to known malicious IPs
* **File System Events:** Unusual DLL deletions in ProgramData
* **Process Behavior:** Suspicious timeout commands before deletion
* **Library Loading:** Unexpected SQLite usage in office documents

---

## References

* [MITRE ATT&CK T1555 - Credentials from Password Stores](https://attack.mitre.org/techniques/T1555/)
* [Any.run Analysis Report](https://any.run/report/a040a0af8697e30506218103074c7d6ea77a84ba3ac1ee5efae20f15530a19bb/d55e2294-5377-4a45-b393-f5a8b20f7d44)
* [CyberDefenders Oski Lab](https://cyberdefenders.org/blueteam-ctf-challenges/oski/)

---

## ⚠️ Disclaimer

> **This writeup is for educational purposes only.**
> 
> The analysis techniques demonstrated should only be applied in authorized environments with proper safety measures in place.
> 
> Always follow your organization's policies and legal requirements when handling malware samples.

