# Lateral Movement Findings (Metasploitable -> DVWA)

## Summary
*After accessing the Metasploitable machine using the same ProFTPD Metasploit exploit used in the [Post Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Post-Exploitation/README.md#metasploit) scenario, lateral movement via SSH was attempted. The goal was to successfully access the DVWA VM which also lives on the VLAN 4. After successful lateral movement only Wazuh detected any kind of traffic. Further tuning will need to be made to the SOC services for enhanced lateral movement detection.*

---

# Lateral Movement

- **Metasploitable -> DVWA IP:** `192.168.4.3 -> 192.168.4.2`
- **[Metasploit Exploit:](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Lateral%20Movement/Evidence/msfexploit.png)** `exploit/unix/ftp/proftpd_modcopy_exec`
- **[Lateral SSH](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Lateral%20Movement/Evidence/ssh.png)** 
- **Executed Metasploit Exploit Timestamp (EST):**  `09/06/2026 @ 14:26`
- **Lateral SSH Timestamp (EST):** `09/06/2026 @ 14:28`

## Zeek


| Field                           | Value                                                                                                                                                                                                        |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Connection between two VLAN 4 hosts? | **No**                                                                                                                                                                                                       |
| Notes                           | There were no connection logs generated after a successful SSH between 192.168.4.3 -> 192.168.4.2. The only connection logs generated was during the initial exploit of 192.168.4.3 via the Kali attack box. |


---



## Suricata


| Field   | Value                                                                                                                                                                                                                                      |
| ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Alerts? | **No**                                                                                                                                                                                                                                     |
| Notes   | The only related alerts generated were for the initial exploit on 192.168.4.3, not when lateral movement was successful via SSH to 192.168.4.2 (DVWA). Other Suricata alerts around this timeframe were unrelated to SSH/lateral movement. |


---



## Wazuh


| Field   | Value                                                   |
| ------- | ------------------------------------------------------- |
| Rule Descriptions  | sshd: authentication success, PAM: Login session opened |
| Rule ID | 5715, 5501                                              |
| Evidence | [SSHD Auth Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Lateral%20Movement/Evidence/wazuh-sshd-alert.png), [PAM Login Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Lateral%20Movement/Evidence/wazuh-pam-alert.png)                                              |

---



## Expected vs Observed


| Expected                      | Observed                                                                         |                                                                                       
| ----------------------------- | -------------------------------------------------------------------------------- | 
| East-West SSH visible on SPAN | Only the Wazuh agent detected any kind of lateral movement and generated alerts. Suricata and Zeek did not generate any kind of logs or alerts related to a successful SSH connection. Possible that intra-VLAN traffic is not being mirrored correctly. |  


---



# Findings Recap

Executing the same metasploit exploit from scenario C to gain access to the Metasploitable machine resulted in the expected alerts and logs generated. Performing lateral movement via SSH only resulted in Wazuh alerts being generated, neither Suricata nor Zeek showed any kind of detection that there was successful lateral movement made.

**Note:** For evidence screenshots click [here](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Lateral%20Movement/Evidence)
