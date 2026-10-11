# Homelab SOC Project

## Summary

*This is a personal homelab Security Operation Center (SOC) project. The objective of this project was to practice both network and host monitoring to detect suspicious activity and potential attacks. Using Suricata, Snort, Zeek, Wazuh, and the ELK stack I practiced monitoring various types of logs to determine if suspicious activity was taking place. Using a Kali Linux virtual machine on my admin PC, I simulated various attacks and suspicious traffic against two different target virtual machines. This included recon scans, SQL injection, command injection, file uploads, Metasploit exploits and various other methods. Using 24 hours worth of normal network traffic logs as a baseline I was able to detect anomalies that indicated suspicious network traffic/attacks.* 

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


| Scenario          | Attack                    | Detected? | 
| ----------------- | ------------------------- | --------- | 
| [Recon](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Recon)             | Nmap Scan                 | **Yes**   | 
| [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation)  | SQLi / CMDi / File Upload | **Yes**   |               
| [Post-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Post-Exploitation) | Metasploit / Hydra / PwnKit | **Yes**   |               
| [Lateral Movement](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Lateral%20Movement)  | East-West SSH             | **Yes**   | 


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

<div align="center">
  
<table>
<tr>
<td width="30%" valign="top">

| Component           | 
| ------------------- | 
| [Networking Brief](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Project%20Components/networking-brief.md#network-security-visibility-brief)    |
| [Kibana Dashboard File](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Project%20Components/dashboard.ndjson)   |
| [Custom Wazuh Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml)        |
| [Detection Use Cases](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Use%20Cases) |
| [Incident Report](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Reports/INC-001.md#security-incident-investigation-report)     |
| [Baseline Snapshot](https://github.com/JonahCaro1/ELK-Wazuh-Detection/tree/main/Project%20Components/Baseline#baseline-snapshot)   |

</td>
<td width="70%" valign="top">
  
<img src="Project Components/SOC-Correlation-Dashboard.png" alt="SOC correlation dashboard" />

<p align="center"><em>Custom Kibana correlation dashboard</em></p>

</td>
</tr>
</table>
