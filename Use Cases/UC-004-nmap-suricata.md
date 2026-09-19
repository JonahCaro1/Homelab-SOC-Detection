# Detection Use Case: Network Scan (Nmap)


| Field                | Value                                                                     |
| -------------------- | ------------------------------------------------------------------------- |
| **Use Case ID**      | UC-004                                                                    |
| **Title**            | Network Scan (Nmap) via Suricata in Wazuh                                 |
| **Author**           | Jonah Caro                                                                |
| **Version**          | 1.1                                                                       |
| **Status**           | **Production (Lab)**                                                      |
| **Last Tested**      | 09/08/2026                                                                |
| **Related Scenario** | [Recon](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Recon#network-recon-findings-dvwa-vm)                                          |
| **Wazuh Rule IDs**   | **100012** (primary, level 10) · **100015** (HTTP 400 fallback, level 10) |


---



## Objective

Give Wazuh a **direct** nmap/scan alert. Scenario A only showed generic **31101** (HTTP 400). With Suricata→Wazuh on `nsm-sensor`, escalate ET SCAN / Nmap signatures into rule **100012**.

- **Primary (100012):** Suricata alert containing `Nmap`
- **Fallback (100015):** ≥5× rule **31101** from the same source IP within 120s

---



## MITRE ATT&CK


| Tactic    | Technique                 | ID    |
| --------- | ------------------------- | ----- |
| Discovery | Network Service Discovery | T1046 |


---



## Data Sources


| Source   | Log type                 | Index / location         |
| -------- | ------------------------ | ------------------------ |
| Suricata | eve.json alerts          | nsm-sensor agent → Wazuh |
| Wazuh    | Optional 31101 frequency | DVWA agent               |
| Zeek     | Connection log spike     | Lab logs                 |


---



## Logic


| Rule       | Level | Condition                                      | Meaning                          |
| ---------- | ----- | ---------------------------------------------- | -------------------------------- |
| **100012** | 10    | Parent Suricata (`86601`) **and** match `Nmap` | Direct scan signature into Wazuh |
| **100015** | 10    | ≥5× **31101** same source in 120s              | HTTP 400 storm fallback          |




### Rule Definition

See [Wazuh Custom Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml): Rules **100012** and **100015**

---



## Alert Severity


| Rule   | Level | Justification                                        |
| ------ | ----- | ---------------------------------------------------- |
| 100012 | 10    | Recon / scan — elevate if targeting prod-like assets |
| 100015 | 10    | Burst of HTTP 400s often accompanies scanning        |


---



## False Positives


| Source                | Rule            | Tuning                       |
| --------------------- | --------------- | ---------------------------- |
| Authorized vuln scans | 100012 / 100015 | Whitelist scanner IP         |
| Lab nmap during demos | 100012          | Accept or maintain allowlist |


---



## Response playbook

1. Confirm signature text contains Nmap / ET SCAN.
2. Note source IP and destination IP.
3. Pivot Kibana Suricata SID (e.g. 2009358) for packet-level detail.
4. Correlate Zeek connection log burst for the same window.

---



## Testing


| Test                       | Expected                         | Last result |
| -------------------------- | -------------------------------- | ----------- |
| `nmap -sV -T4 192.168.4.2` | **100012** in Wazuh              | **Pass**    |
| Same scan                  | Optional **100015** if many 400s | **Pass**    |


---



## Metrics


| Metric        | Target  | Actual  |
| ------------- | ------- | ------- |
| MTTD (100012) | < 1 min | < 1 min |


---



## References

- Scenario: [Recon](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Recon#network-recon-findings-dvwa-vm)
- Rules: [Custom Wazuh Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml)
- Gap Closed: Wazuh only had HTTP 400, not a direct scan alert
- Residual Gap: Scans that evade Suricata Nmap signatures may only hit 100015 or Zeek volume

