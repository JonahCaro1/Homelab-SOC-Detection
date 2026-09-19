# Detection Use Case: Command Injection


| Field                | Value                                                            |
| -------------------- | ---------------------------------------------------------------- |
| **Use Case ID**      | UC-001                                                           |
| **Title**            | DVWA Command Injection (URI high-confidence + POST telemetry)    |
| **Author**           | Jonah Caro                                                       |
| **Version**          | 1.1                                                              |
| **Status**           | **Production (Lab)**                                             |
| **Last Tested**      | 09/08/2026                                                       |
| **Related Scenario** | [Web-Exploitation: Command injection](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#command-injection)                              |
| **Wazuh Rule IDs**   | **100016** (primary, level 12) · **100010** (telemetry, level 3) |


---



## Objective

Detect command injection on DVWA targer with host telemetry using a **split-confidence** model:

- **High confidence (100016):** injection tokens visible in the request URI with `/vulnerabilities/exec`
- **Low confidence / telemetry (100010):** any POST to the exec form (access.log cannot see POST body)

Apache access.log does **not** include POST bodies, so Wazuh cannot tell benign `8.8.8.8` from `8.8.8.8; whoami` on POST alone.

---



## MITRE ATT&CK


| Tactic         | Technique                         | ID    |
| -------------- | --------------------------------- | ----- |
| Execution      | Command and Scripting Interpreter | T1059 |
| Initial Access | Exploit Public-Facing Application | T1190 |


---



## Data Sources


| Source   | Log type                                       | Index / location |
| -------- | ---------------------------------------------- | ---------------- |
| Wazuh    | Apache access log (DVWA agent)                 | wazuh-alerts-*   |
| Zeek     | http.log (POST /exec/)                         | Lab logs         |
| Suricata | Often no alert for POST-body Command Injection | N/A              |


---



## Logic


| Rule       | Level | Condition                                                                  | Meaning                                |
| ---------- | ----- | -------------------------------------------------------------------------- | -------------------------------------- |
| **100016** | 12    | `vulnerabilities/exec` **and** URI tokens (`%3B`, `whoami`, `passwd`, `;`) | Match injection like tokens in the URL |
| **100010** | 3     | `vulnerabilities/exec` **and** `POST`                                      | Form submitted, payload unknown        |




### Rule Definition

See [Custom Wazuh Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml): Rules **100016** and **100010**

---



## Alert Severity


| Rule   | Level | Justification                                               |
| ------ | ----- | ----------------------------------------------------------- |
| 100016 | 12    | Tokens in URI strongly indicate injection                   |
| 100010 | 3     | Benign ping and malicious POST look identical in access.log |


---



## False Positives


| Source                            | Rule   | Tuning                        |
| --------------------------------- | ------ | ----------------------------- |
| POST `8.8.8.8` (normal DVWA ping) | 100010 | Accept as level-3 telemetry   |
| Lab demos with tokens in URL      | 100016 | Expected true positive in lab |


---



## Response playbook

1. If **100016**: Treat as likely Command Injection. Capture full URL, source IP and timestamp.
2. If **only 100010**: Check Zeek HTTP logs for same window, do not assume an injection attack.
3. Pivot: `event.module: zeek and event.original: *vulnerabilities/exec*`
4. Confirm source, and check Web-Exploitation scenario operator log.
5. Note Suricata may still be silent for POST-body Command Injection.

---



## Testing


| Test                                              | Expected                                        | Last result |
| ------------------------------------------------- | ----------------------------------------------- | ----------- |
| POST `8.8.8.8` only                               | **100010** (lvl 3), **No 100016**               | **Pass**    |
| POST `8.8.8.8; cat /etc/passwd`                   | **100010** (lvl 3), **No 100016** (body hidden) | **Pass**    |
| Request with exec + `;`/`whoami`/`%3B` in **URL** | **100016** (lvl 12)                             | **Pass**    |
| Browse DVWA home only                             | Neither rule                                    | **Pass**    |
| Open exec page GET (no POST)                      | Neither rule                                    | **Pass**    |


---



## Metrics


| Metric                                           | Target                    | Actual  |
| ------------------------------------------------ | ------------------------- | ------- |
| MTTD (100016)                                    | < 1 min                   | < 1 min |
| Falso positive rate on benign POST (as “attack”) | 0 if filtering level ≥ 10 | 0       |


---



## References

- Scenario: [Web-Exploitation](https://github.com/JonahCaro1/Homelab-SOC-Detection/tree/main/Scenarios/Web-Exploitation#command-injection)
- Rules: [Wazuh Custom Rules](https://github.com/JonahCaro1/Homelab-SOC-Detection/blob/main/Project%20Components/custom-wazuh-rules.xml)
- Gap Closed: No useful Wazuh signal on Command Injection
- Residual Gap: POST-body payloads still not visible to Wazuh

