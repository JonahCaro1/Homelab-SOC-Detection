# Detection Use Case: SQL Injection


| Field                | Value                           |
| -------------------- | ------------------------------- |
| **Use Case ID**      | UC-002                          |
| **Title**            | DVWA SQL Injection URI Patterns |
| **Author**           | Jonah Caro                      |
| **Version**          | 1.1                             |
| **Status**           | **Production (Lab)**            |
| **Last Tested**      | 09/08/2026                      |
| **Related Scenario** | [Web-Exploitation: SQL Injection](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#sql-injection) |
| **Wazuh Rule IDs**   | **100013** (Level 12)           |


---



## Objective

Detect SQL injection against DVWA with a **specific** rule (URI patterns on `/vulnerabilities/sqli`), replacing reliance on generic rule **31106** (“web attack returned 200”).

---



## MITRE ATT&CK


| Tactic         | Technique                         | ID    |
| -------------- | --------------------------------- | ----- |
| Initial Access | Exploit Public-Facing Application | T1190 |


---



## Data Sources


| Source   | Log type                | Index / location     |
| -------- | ----------------------- | -------------------- |
| Wazuh    | Apache access log       | wazuh-alerts-*       |
| Suricata | ET WEB_SERVER SQLi SIDs | eve.json → Wazuh/ELK |
| Zeek     | http.log with SQLi URI  | Lab logs             |


---



## Logic


| Rule       | Level | Condition                                                                 | Meaning                                         |
| ---------- | ----- | ------------------------------------------------------------------------- | ----------------------------------------------- |
| **100013** | 12    | `/vulnerabilities/sqli` **and** URI tokens (`union`, `%27`, `or+1`, etc.) | SQL injection patterns visible in the request line |




### Rule Definition

See [Wazuh Custom Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml): Rule **100013**

---



## Alert Severity


| Rule   | Level | Justification                                        |
| ------ | ----- | ---------------------------------------------------- |
| 100013 | 12    | Confirmed SQLi probe patterns against known vuln app |


---



## False Positives


| Source                                  | Rule   | Tuning                                     |
| --------------------------------------- | ------ | ------------------------------------------ |
| Benign query params containing “select” | 100013 | Narrow match or require `union` + `select` |


---



## Response Playbook

1. Validate rule id **100013** (not only 31106).
2. Extract `url` / payload from alert.
3. Pivot Suricata SID (e.g. 2010963) and Zeek HTTP logs for same source IP.
4. Confirm source against Web-Exploitation scenario operator log.

---



## Testing


| Test                                         | Expected         | Last result |
| -------------------------------------------- | ---------------- | ----------- |
| `1' UNION SELECT user, password FROM users#` | **100013** fires | **Pass**    |
| Normal DVWA sqli page load without payload   | No 100013        | **Pass**    |


---



## Metrics


| Metric        | Target  | Actual  |
| ------------- | ------- | ------- |
| MTTD (100013) | < 1 min | < 1 min |


---



## References

- Scenario: [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#sql-injection)
- Rules: [Wazuh Custom Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml)
- Gap Closed: Generic 31106-only SQLi detection
- Residual Gap: Encoded/obfuscated payloads may need ongoing token tuning

