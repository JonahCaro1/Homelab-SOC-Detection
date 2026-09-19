# Detection Use Case: File Upload / Webshell


| Field                | Value                                                              |
| -------------------- | ------------------------------------------------------------------ |
| **Use Case ID**      | UC-003                                                             |
| **Title**            | DVWA Uploaded PHP / Webshell Access                                |
| **Author**           | Jonah Caro                                                         |
| **Version**          | 1.1                                                                |
| **Status**           | **Production (Lab)**                                               |
| **Last Tested**      | 09/08/2026                                                         |
| **Related Scenario** | [Web-Exploitation: File Upload](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#file-upload)                                      |
| **Wazuh Rule IDs**   | **100011** (Primary, level 12) · **100014** (Upload page, level 6) |


---



## Objective

Detect access to PHP files under DVWA `hackable/uploads/` (webshell execution path). File upload in the Web-Exploitation scenario showed Suricata ATTACK_RESPONSE on `/etc/passwd` output but **no Wazuh alert** for the host path.

- **High confidence (100011):** request to `hackable/uploads` with `.php`
- **Earlier signal (100014):** access to the upload page (`vulnerabilities/upload`)

---



## MITRE ATT&CK


| Tactic         | Technique                            | ID        |
| -------------- | ------------------------------------ | --------- |
| Persistence    | Server Software Component: Web Shell | T1505.003 |
| Initial Access | Exploit Public-Facing Application    | T1190     |


---



## Data sources


| Source   | Log type                    | Index / location |
| -------- | --------------------------- | ---------------- |
| Wazuh    | Apache access log           | wazuh-alerts-*   |
| Suricata | ATTACK_RESPONSE passwd SIDs | eve.json         |
| Zeek     | GET to uploaded `.php`      | Lab logs         |


---



## Logic


| Rule       | Level | Condition                                   | Meaning                        |
| ---------- | ----- | ------------------------------------------- | ------------------------------ |
| **100011** | 12    | `hackable/uploads` **and** `php` in request | Uploaded PHP / webshell access |
| **100014** | 6     | `vulnerabilities/upload`                    | Upload UI accessed             |




### Rule Definition

See [Custom Wazuh Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml): Rules **100011** and **100014**

Detects **access** to uploaded PHP, not response-body content. Suricata remains stronger for passwd-in-response.

---



## Alert Severity


| Rule   | Level | Justification         |
| ------ | ----- | --------------------- |
| 100011 | 12    | Webshell-class access |
| 100014 | 6     | Upload UI access      |


---



## False Positives


| Source                      | Rule   | Tuning               |
| --------------------------- | ------ | -------------------- |
| Lab cleanup listing uploads | 100011 | Time-bound or accept |
| Browsing upload form only   | 100014 | Expected telemetry   |


---



## Response Playbook

1. If **100011**: note filename in `url` (`test.php`), treat as likely webshell access.
2. Correlate Suricata passwd ATTACK_RESPONSE at the same timestamp.
3. Pivot Zeek HTTP for the same URI / source IP.
4. Optional: File Integrity Monitoring on the upload directory for new `.php` files.

---



## Testing


| Test                              | Expected         | Last result |
| --------------------------------- | ---------------- | ----------- |
| Upload PHP webshell + GET execute | **100011** fires | **Pass**    |
| Open upload page only             | **100014** fires | **Pass**    |
| Upload non-PHP (e.g. jpeg) only   | No 100011        | **Pass**    |


---



## Metrics


| Metric        | Target  | Actual  |
| ------------- | ------- | ------- |
| MTTD (100011) | < 1 min | < 1 min |


---



## References

- Scenario: [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#file-upload)
- Rules: [Custom Wazuh Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml)
- Gap Closed: No Wazuh alert on file upload / webshell access
- Residual Gap: Rule detects access path, not shell command output in HTTP body

