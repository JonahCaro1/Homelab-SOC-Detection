# Network Recon Findings (DVWA VM)

## Summary

*Conducted two different nmap scans from Kali VM (192.168.6.180) to DVWA (192.168.4.2). One scan was at 14:43 PM EST and the other at 14:54 PM EST. Zeek, Suricata, Snort and Wazuh all showed indicators of suspicious network activity.*

---

# Nmap Scan

- **Attack -> Target IP:** `192.168.6.180 -> 192.168.4.3`
- **[Nmap:](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Recon/Evidence/nmap%20scan.png)** `nmap -sV -T4 192.168.4.2`
- **Scan Timestamps (EST):** `09/01/2026 @ 14:43 EST` | `09/01/2026 @ 14:54 EST`


## Zeek


| Field                         | Value                                  |
| -------------------------------- | --------------------------------------- |
| Log spikes during attack window? | **Yes**                                 |
| Protocols / Ports Observed       | *TCP Ports: 22, 80, 100, 1165, 5950* |
| Evidence       | [Filtered Connection Logs](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Recon/Evidence/zeek-postnmap-ipfilter-1hr.png) |

---



## Suricata


| Field          | Value                                                                     |
| -------------- | ------------------------------------------------------------------------- |
| SID      | 2009358                                                                   |
| Signature Name | ET SCAN Nmap Scripting Engine User-Agent Detected (Nmap Scripting Engine) |
| Evidence    | [Alert Log](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Recon/Evidence/suricata-alert-example.png)                                                                        |

---



## Wazuh


| Field                      | Value                      | 
| -------------------------- | -------------------------- | 
| Alert Description           | Web server 400 error code. | 
| Alert ID                    | 31101                      |                
| Evidence | [Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Recon/Evidence/wazuh-alert.png) |
| Notes | Multiple web server 400 errors may indicate nmap scan. | 

---



## Snort


| Field                 | Value                                                           |
| --------------------- | --------------------------------------------------------------- |
| Alerts during window? | **Yes**                                                         |
| Notes                 | Logs spiked up to 2,519 from the average 500 during nmap scans. |


---



## Expected vs observed


| Expected                  | Observed                                                                      | 
| ------------------------- | ----------------------------------------------------------------------------- | 
| Port scan in Suricata     | Multiple nmap alerts were triggered.                                          | 
| Connection log spike in Zeek        | There was a large spike in connection logs during both nmap scans. Did not label source or destination ports in the fields and logs were generic.           |  
| Wazuh alert on DVWA agent | There were multiple alerts for web error 400. This may indicate an nmap scan but is not a direct claim. |


---



## Findings Recap

When comparing the Zeek 24hr baseline to the records after the nmap scan, spikes of connection records can be seen during the exact times of the nmap scans. Viewing the Suricata alerts provided a signature ID of 2009358 with the name "ET SCAN Nmap Scripting Engine User-Agent Detected (Nmap Scripting Engine)". Wazuh threat hunting alerts on the agent confirm suspicious activity as there are clear event spikes at the moments of the nmap scan. A Wazuh rule ID of 31101 just indicates a web server 400 error. However, given the multiple alerts and the timeframe this may indicate an nmap scan. The provided data confirms nmap related activity on the network. 
