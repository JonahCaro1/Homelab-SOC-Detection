# Lateral movement

**MITRE:** T1021 — Remote Services  
**Status:** **Completed**

From a shell on one VLAN 4 host, attempted SSH to another VLAN 4 host.

---

## Pre-Flight


| Field                             | Value         |
| --------------------------------- | ------------- |
| Date                              | 09/06/2026    |
| Metasploitable (Compromised Host) | *192.168.4.3* |
| DVWA (Destination)                | *192.168.4.2* |
| Method                            | SSH           |


---

## Attack Log


| #   | EST Start | EST End | Command                                                                                                         |
| --- | --------- | ------- | --------------------------------------------------------------------------------------------------------------- |
| 1   | 14:28     | 14:28   | `HOME=/tmp ssh -i /tmp/id_ed25519 -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null dvwa@192.168.4.2` |


---



## MTTD


| Attack start       | First alert           | Rule / SID     | MTTD (Min) |
| ------------------ | --------------------- | -------------- | ---------- |
| 09/06/2026 @ 14:28 | 09/06/2026 @ 14:28:48 | Wazuh ID: 5501 | < 1 min    |


---



## Operator notes

*Only Wazuh was able to detect the East-West traffic. Adjustments will have to be made in order to ensure full detection.* 