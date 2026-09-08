# Detection Use Case — UC-004


| Field                | Value                                        |
| -------------------- | -------------------------------------------- |
| **Use case ID**      | UC-004                                       |
| **Title**            | Network Scan (Nmap) via Suricata in Wazuh    |
| **Author**           | Jonah Caro                                   |
| **Version**          | 1.0                                          |
| **Status**           | **Production (Lab)**                         |
| **Last tested**      | 09/08/2026                                   |
| **Related scenario** | A — Network reconnaissance                   |
| **Wazuh rule ID**    | 100012 (primary), 100015 (HTTP 400 fallback) |


---



## 1. Objective

Give Wazuh a **direct** nmap/scan alert. Scenario A only showed generic **31101** (HTTP 400). With Suricata→Wazuh on `nsm-sensor`, escalate ET SCAN / Nmap signatures into rule **100012**.

---



## 2. MITRE ATT&CK


| Tactic    | Technique                 | ID    |
| --------- | ------------------------- | ----- |
| Discovery | Network Service Discovery | T1046 |


---



## 3. Data sources


| Source   | Log type                 | Index / location         |
| -------- | ------------------------ | ------------------------ |
| Suricata | eve.json alerts          | nsm-sensor agent → Wazuh |
| Wazuh    | Optional 31101 frequency | DVWA agent               |
| Zeek     | conn spike               | Lab logs                 |


---



## 4. Logic



### 4.1 Rule definition

- **100012**: parent Suricata alert (`86601`), match `Nmap|ET SCAN|...`
- **100015**: ≥5× rule **31101** from same source in 120s (fallback)



### 4.2 If 100012 never fires

On manager, confirm Suricata parent rule id:

```bash
grep -R "suricata" /var/ossec/ruleset/rules/*suricata* | head
```

Adjust `<if_sid>86601</if_sid>` if Wazuh version uses a different parent.

---



## 5. Alert severity


| Level | Justification                                        |
| ----- | ---------------------------------------------------- |
| 10    | Recon / scan — elevate if targeting prod-like assets |


---



## 6. False positives


| Known FP source       | Tuning action                |
| --------------------- | ---------------------------- |
| Authorized vuln scans | Whitelist scanner IP         |
| Lab nmap during demos | Accept or maintain allowlist |


---



## 7. Response playbook

1. Confirm signature text contains Nmap / ET SCAN
2. Note src_ip (Kali) and dest (VLAN 40)
3. Pivot Kibana Suricata SID (e.g. 2009358) for packet-level detail

---



## 8. Test


| Test                       | Expected result                  | Last result |
| -------------------------- | -------------------------------- | ----------- |
| `nmap -sV -T4 192.168.4.2` | **100012** in Wazuh              | **Pass**    |
| Same scan                  | Optional **100015** if many 400s | **Pass**    |


---



## 9. Metrics


| Metric | Target  | Actual  |
| ------ | ------- | ------- |
| MTTD   | < 1 min | < 1 min |


---



## 10. References

- Scenario: `scenarios/A-recon/`
- Gap closed: Wazuh only had HTTP 400, not direct scan alert

