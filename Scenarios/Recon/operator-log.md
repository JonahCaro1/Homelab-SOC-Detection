# Network Reconnaissance

**MITRE:** T1046 — Network Service Discovery  
**Status:** Complete

---

## Pre-Flight


| Field                 | Value                  |
| --------------------- | ---------------------- |
| Date                  | 09/01/2026             |
| Attacker              | Kali VM: 192.168.6.180 |
| Target                | *DVWA: 192.168.4.2*    |
| Wazuh agent on target | DVWA                   |
| Analyst               | Jonah Caro             |


---

## Attack Log


| #   | EST Start | EST End     | Command                    |
| --- | --------- | ----------- | -------------------------- |
| 1   | 14:43     | 14:43 (16s) | `nmap -sV -T4 192.168.4.2` |
| 2   | 14:54     | 14:54 (7s)  | `nmap -sV -T4 192.168.4.2` |


---



## MTTD Calculation


| Event                   | Time (EST)                                                  |
| ----------------------- | ----------------------------------------------------------- |
| Attack started (run #1) | 14:43                                                       |
| First alert fired       | 14:43:38                                                    |
| Platform of first alert | Suricata                                                    |
| Rule / SID              | ET SCAN Nmap Scripting Engine User-Agent Detected (2009358) |
| **MTTD (minutes)**      | < 1 min                                                     |


---



## Operator Notes

*Operations appear to be working properly, Suricata logged alerts at both times of the nmap scans. Spikes in Zeek and Snort logs were also expected and did occur.*  