# Detection Use Case — UC-003


| Field                | Value                                           |
| -------------------- | ----------------------------------------------- |
| **Use case ID**      | UC-003                                          |
| **Title**            | DVWA Uploaded PHP / Webshell Access             |
| **Author**           | Jonah Caro                                      |
| **Version**          | 1.0                                             |
| **Status**           | **Production (Lab)**                            |
| **Last tested**      | 09/08/2026                                      |
| **Related scenario** | B — File upload                                 |
| **Wazuh rule ID**    | 100011 (primary), 100014 (upload page accessed) |


---



## 1. Objective

Detect access to PHP files under DVWA `hackable/uploads/` (webshell execution path). Scenario B showed Suricata ATTACK_RESPONSE on /etc/passwd output but **no Wazuh alert**.

---



## 2. MITRE ATT&CK


| Tactic         | Technique                            | ID        |
| -------------- | ------------------------------------ | --------- |
| Persistence    | Server Software Component: Web Shell | T1505.003 |
| Initial Access | Exploit Public-Facing Application    | T1190     |


---



## 3. Data sources


| Source   | Log type                    | Index / location |
| -------- | --------------------------- | ---------------- |
| Wazuh    | Apache access log           | wazuh-alerts-*   |
| Suricata | ATTACK_RESPONSE passwd SIDs | eve.json         |
| Zeek     | GET to uploaded `.php`      | Lab logs         |


---



## 4. Logic



### 4.1 Rule definition

- **100011**: `/hackable/uploads/` + `.php` in request  
- **1000111**: `/vulnerabilities/upload` page access (earlier signal)



### 4.2 Limitations

- Detects **access** to uploaded PHP, not file content in response body  
- Suricata still better for passwd-in-response; this closes the **host** gap

---



## 5. Alert severity


| Level       | Justification         |
| ----------- | --------------------- |
| 12 (100011) | Webshell-class access |
| 6 (100014)  | Upload UI access      |


---



## 6. False positives


| Known FP source             | Tuning action        |
| --------------------------- | -------------------- |
| Lab cleanup listing uploads | Time-bound or accept |


---



## 7. Response playbook

1. Note filename in `url` (e.g. `test.php`)
2. Correlate Suricata passwd ATTACK_RESPONSE at same timestamp
3. FIM stretch: watch upload directory for new `.php`

---



## 8. Test plan


| Test                              | Expected result  | Last result |
| --------------------------------- | ---------------- | ----------- |
| Upload PHP webshell + GET execute | **100011** fires | **Pass**    |
| Open upload page only             | **100014** fires | **Pass**    |


---



## 9. Metrics


| Metric | Target  | Actual  |
| ------ | ------- | ------- |
| MTTD   | < 1 min | < 1 min |


---



## 10. References

- Scenario: `scenarios/B-dvwa-web/`
- Gap closed: No Wazuh alert on file upload / webshell

