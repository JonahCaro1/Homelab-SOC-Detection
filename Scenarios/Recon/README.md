# Network Recon Findings (DVWA VM)

## Summary

*Conducted two different nmap scans from my Kali VM (192.168.6.180) to DVWA (192.168.4.2). One scan was at 14:43PM and the other at 14:54PM. Zeek, Suricata,* Snort and Wazuh all showed indicators of suspicious network activity. 

---

## Zeek


| Question                         | Answer                                  |
| -------------------------------- | --------------------------------------- |
| Conn spike during attack window? | **Yes**                                 |
| Source → destination             | *192.168.6.180 → 192.168.4.2*           |
| Protocols / ports observed       | *TCP Ports: 22, 80, 100, 1165, 5950...* |


---



## Suricata


| Field          | Value                                                                     |
| -------------- | ------------------------------------------------------------------------- |
| Alerts fired?  | **Yes**                                                                   |
| First SID      | 2009358                                                                   |
| Signature name | ET SCAN Nmap Scripting Engine User-Agent Detected (Nmap Scripting Engine) |
| Alert count    | 18                                                                        |


---



## Wazuh


| Field                      | Value                      | **Notes**                                              |
| -------------------------- | -------------------------- | ------------------------------------------------------ |
| Alerts on target agent?    | **Yes**                    |                                                        |
| Rule ID                    | 31101                      |                                                        |
| Rule description           | Web server 400 error code. | Multiple web server 400 errors may indicate nmap scan. |
| Correlates with scan time? | **Yes**                    | Sep 1, 2026 @ 14:43:39.386                             |


---



## Snort


| Field                 | Value                                                           |
| --------------------- | --------------------------------------------------------------- |
| Alerts during window? | **Yes**                                                         |
| Notes                 | Logs spiked up to 2,519 from the average 500 during nmap scans. |


---



## Expected vs observed


| Expected                  | Observed                                                                      | Gap                                                                                            |
| ------------------------- | ----------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Port scan in Suricata     | Multiple nmap alerts were triggered.                                          | None, detection met expectations.                                                              |
| Conn spike in Zeek        | There was a large spike in connection logs during both nmap scans.            | Did not label source or destination ports in the fields. Generic, does not indicate nmap scan. |
| Wazuh alert on DVWA agent | There were multiple alerts for web error 400. This may indicate an nmap scan. | An HTTP 400 error although may allude to an nmap scan is not a direct claim and only partial.  |


---



## Findings Recap

When comparing the Zeek 24hr baseline to the records after the nmap scan, spikes of connection records can be seen during the exact times of the nmap scans. Viewing the Suricata alerts provided a signature ID of 2009358 with the name "ET SCAN Nmap Scripting Engine User-Agent Detected (Nmap Scripting Engine)". Wazuh threat hunting alerts on the agent confirm suspicious activity as there are clear event spikes at the moments of the nmap scan. A Wazuh rule ID of 31101 just indicates a web server 400 error. However, given the multiple alerts and the timeframe this may indicate an nmap scan. The provided data confirms nmap related activity on the network. 
