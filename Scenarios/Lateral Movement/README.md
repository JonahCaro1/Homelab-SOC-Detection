# Lateral Movement Findings (Metasploitable -> DVWA)

- **Executed Metasploit Exploit:**  `09/06/2026 @ 14:26 EST`
- **Lateral SSH:** `Metasploitable -> DVWA | 09/06/2026 @ 14:28 EST`
- **Metasploitable IP:** `192.168.4.3`
- **DVWA:** `192.168.4.2`

---

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
| Alerts  | sshd: authentication success, PAM: Login session opened |
| Rule ID | 5715, 5501                                              |


---



## Expected vs observed


| Expected                      | Observed                                                                         | Gap                                                                                                                                                                     |
| ----------------------------- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| East-west SSH visible on SPAN | Only the Wazuh agent detected any kind of lateral movement and generated alerts. | Suricata and Zeek did not generate any kind of logs or alerts related to a successful SSH connection. Possible that intra-VLAN traffic is not being mirrored correctly. |


---



# Findings Recap

Executing the same metasploit exploit from scenario C to gain access to the Metasploitable machine resulted in the expected alerts and logs generated. Performing lateral movement via SSH only resulted in Wazuh alerts being generated, neither Suricata nor Zeek showed any kind of detection that there was successful lateral movement made.
