# Attack Scenarios

---

## Index


| Name              | MITRE Techniques                         | Status          |
| ----------------- | ---------------------------------------- | --------------- |
| Recon             | T1046: Network Service Discovery         | **Complete**    |
| Web-Exploitation  | T1190: Exploit Public-Facing Application | **Complete**    |
| Post-Exploitation | T1110 / T1059 / T1068 / T1505.003        | **In Progress** |
| Lateral Movement  | T1021: Remote Services                   | **Complete**    |


---

## Attack Log


| Scenario          | Attack                                                                                | Start (EST)        | End (EST) | Attacker                     | Target                       | Wazuh agent    |
| ----------------- | ------------------------------------------------------------------------------------- | ------------------ | --------- | ---------------------------- | ---------------------------- | -------------- |
| Recon             | Nmap: `nmap -sV -T4 192.168.4.2`                                                      | 09/01/2026 @ 14:43 | 14:43     | Kali *192.168.6.180*         | DVWA *192.168.4.2*           | DVWA           |
| Web-Exploitation  | SQLi: `1' UNION SELECT user, password FROM users#`                                    | 09/01/2026 @ 22:32 | 22:32     | Kali *192.168.6.180*         | DVWA *192.168.4.2*           | DVWA           |
| Web-Exploitation  | Command Injection: `8.8.8.8; cat /passwd/etc`                                         | 09/01/2026@ 23:11  | 23:11     | Kali *192.168.6.180*         | DVWA *192.168.4.2*           | DVWA           |
| Web-Exploitation  | File Upload: `test.php`                                                               | 09/02/2026 @ 00:55 | 00:55     | Kali *192.168.6.180*         | DVWA *192.168.4.2*           | DVWA           |
| Post-Exploitation | Metasploit: `exploit/unix/ftp/proftpd_modcopy_exec`                                   | 09/03/2026 @ 22:55 | 22:55     | Kali *192.168.6.180*         | Metasploitable *192.168.4.3* | Metasploitable |
| Post-Exploitation | Brute Force: `hydra -l vagrant -P /usr/share/wordlists.rockyou.txt ssh://192.168.4.3` | 09/06/2026 @ 15:43 | 15:47     | Kali *192.168.6.180*         | Metasploitable *192.168.4.3* | Metasploitable |
| Post-Exploitation | Privilege Escalation:                                                                 |                    |           | Kali *192.168.6.180*         | Metasploitable *192.168.4.3* | Metasploitable |
| Lateral Movement  | SSH Metasploitable → DVWA                                                             | 09/06/2026 @ 14:26 | 14:26     | Metasploitable *192.168.4.3* | DVWA *192.168.4.2*           | DVWA           |


---



## Detection matrix


| Scenario          | Zeek Evidence | Suricata Evidence | Wazuh evidence | MTTD (min) | First Alert |
| ----------------- | ------------- | ----------------- | -------------- | ---------- | ----------- |
| Recon             |               |                   |                | < 1Min     |             |
| Web-Exploitation  |               |                   |                | < 1 Min    |             |
| Post-Exploitation |               |                   |                | < 1 Min    |             |
| Lateral Movement  |               |                   |                | < 1 Min    |             |


**MTTD** = minutes from attack start (operator log) to first high-severity alert.

---



## Expected vs observed


| Scenario          | Expected Detection | Observed | Gap / Notes |
| ----------------- | ------------------ | -------- | ----------- |
| Recon             |                    |          |             |
| Web-Exploitation  |                    |          |             |
| Post-Exploitation |                    |          |             |
| Lateral Movement  |                    |          |             |


---
