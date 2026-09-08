# Detection Use Case — UC-002


| Field                | Value                           |
| -------------------- | ------------------------------- |
| **Use case ID**      | UC-002                          |
| **Title**            | DVWA SQL Injection URI Patterns |
| **Author**           | Jonah Caro                      |
| **Version**          | 1.0                             |
| **Status**           | **Production (Lab)**            |
| **Last tested**      | 09/08/2026                      |
| **Related scenario** | B — SQL injection               |
| **Wazuh rule ID**    | 100013                          |


---



## 1. Objective

Detect SQL injection against DVWA with a **specific** rule (URI patterns on `/vulnerabilities/sqli`), replacing reliance on generic rule **31106** (“web attack returned 200”).

---



## 2. MITRE ATT&CK


| Tactic         | Technique                         | ID    |
| -------------- | --------------------------------- | ----- |
| Initial Access | Exploit Public-Facing Application | T1190 |


---



## 3. Data sources


| Source   | Log type                | Index / location     |
| -------- | ----------------------- | -------------------- |
| Wazuh    | Apache access log       | wazuh-alerts-*       |
| Suricata | ET WEB_SERVER SQLi SIDs | eve.json → Wazuh/ELK |
| Zeek     | http.log with SQLi URI  | Lab logs             |


---



## 4. Logic



### 4.1 Rule definition

Rule **100013**: parent `31100`, URL contains `/vulnerabilities/sqli`, match `union|select|%27|...`.

### 4.2 Thresholds and filters

- Fires on encoded and plain SQLi tokens in the request line (visible in access.log)

---



## 5. Alert severity


| Level | Justification                                        |
| ----- | ---------------------------------------------------- |
| 12    | Confirmed SQLi probe patterns against known vuln app |


---



## 6. False positives


| Known FP source                         | Tuning action                              |
| --------------------------------------- | ------------------------------------------ |
| Benign query params containing “select” | Narrow match or require `union` + `select` |


---



## 7. Response playbook

1. Validate rule id **100013** (not only 31106)
2. Extract `url` / payload from alert
3. Pivot Suricata SID (e.g. 2010963) and Zeek http for same source IP

---



## 8. Test plan


| Test                                         | Expected result  | Last result |
| -------------------------------------------- | ---------------- | ----------- |
| `1' UNION SELECT user, password FROM users#` | **100013** fires | **Pass**    |
| Normal DVWA sqli page load without payload   | No 100013        | **Pass**    |


---



## 9. Metrics


| Metric | Target  | Actual  |
| ------ | ------- | ------- |
| MTTD   | < 1 min | < 1 min |


---



## 10. References

- Scenario: `scenarios/B-dvwa-web/`
- Gap closed: Generic 31106-only SQLi detection

