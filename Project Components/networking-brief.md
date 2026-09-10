# Network Security Visibility Brief


| Field             | Value      |
| ----------------- | ---------- |
| **Author**        | Jonah Caro |
| **Date**          | 09/07/2026 |
| **Lab reference** |            |


---

## Topology Summary


| VLAN | Name       | Purpose                 | Gateway |
| ---- | ---------- | ----------------------- | ------- |
| 1    | Management | Proxmox Hosts, Admin PC | UDR7    |
| 2    | Services   | Wazuh, ELK, OpenVAS     | UDR7    |
| 4    | Targets    | DVWA, Metasploitable    | UDR7    |
| 6    | Attack     | Kali                    | UDR7    |


---



## Traffic Paths



### Attack Path

```text
Kali (VLAN 6) ──[Allowed: 6 → 4]──► Target (VLAN 4)
                         │
                         ▼ SPAN mirror
                   NSM Sensor → ELK
Target agent ──[Allowed: 4 → 2 agent port]──► Wazuh Manager
```



### Firewall


| Blocked Path              | Security Benefit                     | Detection Implication                                   |
| ------------------------- | ------------------------------------ | ------------------------------------------------------- |
| VLAN 4 → 2 (except agent) | Targets can't pivot to SIEM          | Attacks stay on target, agent telemetry still flows out |
| VLAN 6 → 2                | Attacker can't disable SIEM directly | Must succeed on target first                            |


---



## SPAN / NSM placement


| Attribute         | Value                            |
| ----------------- | -------------------------------- |
| SPAN source ports | Proxmox trunk (VLAN 4/6 traffic) |
| Sensor location   | NSM VM on Proxmox                |
| Tools             | Zeek, Suricata, Snort            |
| Log destination   | Logstash → Elasticsearch         |


---



## Visibility


| Question                                    | Answer                                               | Evidence                                                                                          |
| ------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Are 6 → 4 TCP sessions visible in Zeek?     | **Yes**                                              | Kibana query: `event.module: zeek and event.dataset: *conn* and 192.168.6.180 and 192.168.4.2`    |
| Do Suricata alerts fire on exploit traffic? | **Yes**                                              | Rule SID: 2011465                                                                                 |
| Can Wazuh see auth failures on target?      | **Yes**                                              | Rule ID: 5760                                                                                     |
| What traffic is **not** visible?            | East-West SSH traffic not detected by most services. | Conducting lateral movement via SSH between hosts on VLAN 4 resulted in only Wazuh firing alerts. |


---



## Detection Gap


| Gap                                           | Compensating control   |
| --------------------------------------------- | ---------------------- |
| East-West detection not visible on NSM tools. | Host-based Wazuh agent |


---

