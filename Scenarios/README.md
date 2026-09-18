# Attack Scenarios

---

## Index


| Name              | MITRE Techniques                         | Status          |
| ----------------- | ---------------------------------------- | --------------- |
| [Recon](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Recon)             | T1046: Network Service Discovery         | **Complete**    |
| [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation)  | T1190: Exploit Public-Facing Application | **Complete**    |
| [Post-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Post-Exploitation) | T1110 / T1059 / T1068 / T1505.003        | **Complete** |
| [Lateral Movement](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Lateral%20Movement)  | T1021: Remote Services                   | **Complete**    |


---

## Attack Log


| Scenario          | Attack                                                                                | Start (EST)        | End (EST) | Attacker                     | Target                       | Wazuh agent    |
| ----------------- | ------------------------------------------------------------------------------------- | ------------------ | --------- | ---------------------------- | ---------------------------- | -------------- |
| [Recon](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Recon#nmap-scan)             | Nmap: `nmap -sV -T4 192.168.4.2`                                                      | 09/01/2026 @ 14:43 | 14:43     | Kali *192.168.6.180*         | DVWA *192.168.4.2*           | DVWA           |
| [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#sql-injection)  | SQLi: `1' UNION SELECT user, password FROM users#`                                    | 09/01/2026 @ 22:32 | 22:32     | Kali *192.168.6.180*         | DVWA *192.168.4.2*           | DVWA           |
| [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#command-injection)  | Command Injection: `8.8.8.8; cat /passwd/etc`                                         | 09/01/2026@ 23:11  | 23:11     | Kali *192.168.6.180*         | DVWA *192.168.4.2*           | DVWA           |
| [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#file-upload)  | File Upload: `test.php`                                                               | 09/02/2026 @ 00:55 | 00:55     | Kali *192.168.6.180*         | DVWA *192.168.4.2*           | DVWA           |
| [Post-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Post-Exploitation#metasploit) | Metasploit: `exploit/unix/ftp/proftpd_modcopy_exec`                                   | 09/03/2026 @ 22:55 | 22:55     | Kali *192.168.6.180*         | Metasploitable *192.168.4.3* | Metasploitable |
| [Post-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Post-Exploitation#ssh-brute-force) | Brute Force: `hydra -l vagrant -P /usr/share/wordlists.rockyou.txt ssh://192.168.4.3` | 09/06/2026 @ 15:43 | 15:47     | Kali *192.168.6.180*         | Metasploitable *192.168.4.3* | Metasploitable |
| [Post-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Post-Exploitation#privilege-escalation) | Privilege Escalation: `PwnKit: CVE-2021-4034`                                         | 09/15/2026 @ 16:08             |  16:08         | Kali *192.168.6.180*         | Metasploitable *192.168.4.3* | Metasploitable |
| [Lateral Movement](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Lateral%20Movement#lateral-movement)  | SSH Metasploitable → DVWA                                                             | 09/06/2026 @ 14:26 | 14:26     | Metasploitable *192.168.4.3* | DVWA *192.168.4.2*           | DVWA           |


---



## Detection Matrix


| Scenario          | Zeek Evidence | Suricata Evidence | Wazuh Evidence | MTTD (Min) | First Alert Timestamp |
| ----------------- | ------------- | ----------------- | -------------- | ---------- | ----------- |
| Recon             | [Connection log spike](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Recon/Evidence/zeek-postnmap-ipfilter-1hr.png) | [Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Recon/Evidence/suricata-alert-example.png) |  [Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Recon/Evidence/wazuh-alert.png)              | < 1 Min    |   09/01/2026 @ 14:43:38          |
| Web-Exploitation (SQLi) | [HTTP Log](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Web-Exploitation/Evidence/SQL%20Injection/zeek-log-overview-sqlinject.png)              | [Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Web-Exploitation/Evidence/SQL%20Injection/suricata-log-sqlinjection.png)                  |  [Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Web-Exploitation/Evidence/SQL%20Injection/wazuh-sql-alert.png)              | < 1 Min    | 09/01/2026 @ 22:32:06            |
| Web-Exploitation (Command Injection) | [HTTP Log](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Web-Exploitation/Evidence/Command%20Injection/zeek-command-inject-log.png)              |      No Alert             |  No Alert              | < 1 Min    |  09/01/2026 @ 23:11:09 (Zeek HTTP Log)           |
| Web-Exploitation (File Upload) | [HTTP Log](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Web-Exploitation/Evidence/File%20Upload/zeek-file-upload-log.png)              | [Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Web-Exploitation/Evidence/File%20Upload/suricata-file-upload-log.png)                  |  No Alert              | < 1 Min    |  09/02/2026 @ 00:55:00.698           |
| Post-Exploitation (Metasploit)  | [HTTP Log](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Post-Exploitation/Evidence/ProFTPD%20Exploit/zeek-proftp-log.png)              | [Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Post-Exploitation/Evidence/ProFTPD%20Exploit/suricata-cmd-execution-log.png)                  |  [ProFTPD Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Post-Exploitation/Evidence/ProFTPD%20Exploit/Wazuh-proftp-alert.png), [Cmd Not Allowed Alert](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Scenarios/Post-Exploitation/Evidence/ProFTPD%20Exploit/Wazuh-cmd-denied.png)              | < 1 Min    |  09/03/2026 @ 22:55:39           |
| Post-Exploitation (Brute Force)  |               |                   |                | < 1 Min    |             |
| Post-Exploitation (Privilege Escalaiton)  |               |                   |                | < 1 Min    |             |
| Lateral Movement  |               |                   |                | < 1 Min    |             |

**MTTD** = Minutes from attack start (operator log) to first high-severity alert.

---



## Expected vs observed


| Scenario          | Expected Detection | Observed | Gap / Notes |
| ----------------- | ------------------ | -------- | ----------- |
| Recon             |                    |          |             |
| Web-Exploitation  |                    |          |             |
| Post-Exploitation |                    |          |             |
| Lateral Movement  |                    |          |             |


---
