# Detection Use Cases

Custom Wazuh detection use cases, each use case follows a standard format: objective, MITRE ATT&CK, data sources, logic, severity, false positives, response playbook, tests, and metrics.

**Author:** Jonah Caro  
**Rules:** [Custom Wazuh Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml)

---

## Index

| ID | Title | Scenario | MITRE | Wazuh rules | Status |
|----|-------|----------|-------|-------------|--------|
| [UC-001](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Use%20Cases/UC-001-dvwa-cmdi.md) | Command Injection | [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#command-injection) | T1059, T1190 | **100016** (12) · **100010** (3) | Production (Lab) |
| [UC-002](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Use%20Cases/UC-002-dvwa-sqli.md) | SQL Injection | [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#sql-injection) | T1190 | **100013** (12) | Production (Lab) |
| [UC-003](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Use%20Cases/UC-003-dvwa-upload.md) | File Upload / Webshell | [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#file-upload) | T1505.003, T1190 | **100011** (12) · **100014** (6) | Production (Lab) |
| [UC-004](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Use%20Cases/UC-004-nmap-suricata.md) | Network Scan (Nmap) | [Recon](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Recon#nmap-scan) | T1046 | **100012** (10) · **100015** (10) | Production (Lab) |
| [UC-005](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Use%20Cases/UC-005-pwnkit.md) | PwnKit Privilege Escalation | [Post-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Post-Exploitation#privilege-escalation) | T1068, T1548 | **100020** (12) · **100021** (6) | Production (Lab) |

---

## Design notes

- **Split confidence** where logs are incomplete (e.g. UC-001: POST body not in Apache access.log → level-3 telemetry + level-12 URI tokens).
- **Host + network correlation** where possible (Wazuh rules + Zeek/Suricata evidence in playbooks).
- **Honest gaps** documented (POST-body CMDi, local LPE invisible to NSM without auditd).
- Rules use 6-digit lab IDs in the `10001x`–`10002x` range under custom Wazuh rules.


