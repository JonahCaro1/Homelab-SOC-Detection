# ELK + Wazuh Detection


| Author             | Jonah Caro                                    |
| ------------------ | --------------------------------------------- |
| **Date Completed** | 09/09/2026                                    |
| **Lab**            | Segmented UniFi Network + Proxmox SOC homelab |


---

## Executive Summary

*This is a personal homelab SOC project containing Suricata, Snort, Zeek, Wazuh and the ELK stack. Using a virtual machine on my admin PC running Kali Linux I simulated various attacks against two different target virtual machines. These attacks included SQL injection, command injection, file uploads and metasploit exploits. Using the configured SOC services I was able to successfully detect suspicious network traffic/attacks.* 

---



## Architecture

```text
VLAN 6 Kali ──► VLAN 4 Targets (DVWA / Metasploitable 3)
                      │
                      ▼ SPAN
                Zeek · Suricata · Snort → ELK
                      │
                      └──► Wazuh Manager
```

---



## Scenarios


| Scenario          | Attack                    | Detected? | Primary Detection |
| ----------------- | ------------------------- | --------- | ----------------- |
| Recon             | Nmap Scan                 | **Yes**   | Suricata          |
| Web-Exploitation  | SQLi / CMDi / File Upload | **Yes**   |                   |
| Post-Exploitation | Metasploit / Hydra /      | **Yes**   |                   |
| Lateral movement  | East-West SSH             | **Yes**   | Wazuh             |


Note: *See [Scenarios](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios) for full details*

---



## Detailed Write-Ups


| Scenario          | Operator Log | Findings | Evidence Folder |
| ----------------- | ------------ | -------- | --------------- |
| Recon             | [Operator Log](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Scenarios/Recon/operator-log.md)             | [Findings](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios/Recon#network-recon-findings-dvwa-vm)         | [Evidence](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios/Recon/Evidence)                |
| Web-Exploitation  | [Operator Log](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Scenarios/Web-Exploitation/operator-log.md)             | [Findings](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios/Web-Exploitation#web-exploitation-findings-dvwa-vm)         | [Evidence](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios/Web-Exploitation/Evidence)                |
| Post-Exploitation | [Operator Log](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Scenarios/Post-Exploitation/operator-log.md)             | [Findings](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios/Post-Exploitation#post-exploitation-findings-metasploitable-vm)         | [Evidence](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios/Post-Exploitation/Evidence)                |
| Lateral Movement  | [Operator Log](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Scenarios/Lateral%20Movement/operator-log.md)             | [Findings](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios/Lateral%20Movement#lateral-movement-findings-metasploitable---dvwa)         | [Evidence](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Scenarios/Lateral%20Movement/Evidence)                |


---



## Key Components


| Component           | Location |
| ------------------- | -------- |
| Networking Brief    | [Location](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Project%20Components/networking-brief.md#network-security-visibility-brief)         |
| Kibana Dashboard File   | [Location](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Project%20Components/dashboard.ndjson)         |
| Custom rules        | Location         |
| Detection use cases | [Location](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Use%20Cases)          |
| Incident report     | [Location](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Reports/INC-001.md#security-incident-investigation-report)         |
| Baseline snapshot   | [Location](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Project%20Components/Baseline#baseline-snapshot)         |


---
