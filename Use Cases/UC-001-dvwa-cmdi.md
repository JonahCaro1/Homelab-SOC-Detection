# Detection Use Case — UC-001


| Field                | Value                                                            |
| -------------------- | ---------------------------------------------------------------- |
| **Use case ID**      | UC-001                                                           |
| **Title**            | DVWA Command Injection (URI high-confidence + POST telemetry)    |
| **Author**           | Jonah Caro                                                       |
| **Version**          | 1.1                                                              |
| **Status**           | **Production (Lab)**                                             |
| **Last tested**      | 09/08/2026                                                       |
| **Related scenario** | B — Command injection                                            |
| **Wazuh rule IDs**   | **100016** (primary, level 12) · **100010** (telemetry, level 3) |


---



## 1. Objective

Detect DVWA command injection with host telemetry using a **split-confidence** model:

- **High confidence (100016):** injection tokens visible in the request URI with `/vulnerabilities/exec`
- **Low confidence / telemetry (100010):** any POST to the exec form (access.log cannot see POST body)

Apache access.log does **not** include POST bodies, so Wazuh cannot tell benign `8.8.8.8` from `8.8.8.8; whoami` on POST alone.

---



## 2. MITRE ATT&CK


| Tactic         | Technique                         | ID    |
| -------------- | --------------------------------- | ----- |
| Execution      | Command and Scripting Interpreter | T1059 |
| Initial Access | Exploit Public-Facing Application | T1190 |


---



## 3. Data sources


| Source   | Log type                          | Index / location |
| -------- | --------------------------------- | ---------------- |
| Wazuh    | Apache access log (DVWA agent)    | wazuh-alerts-*   |
| Zeek     | http.log (POST /exec/)            | Lab logs         |
| Suricata | Often no alert for POST-body CMDi | —                |


---



## 4. Logic



### 4.1 Approach (B + C)


| Rule       | Level | Condition                                                               | Meaning                         |
| ---------- | ----- | ----------------------------------------------------------------------- | ------------------------------- |
| **100016** | 12    | `vulnerabilities/exec` **and** URI tokens (`%3B`, `whoami`, `passwd`, ` | `, etc.)                        |
| **100010** | 3     | `vulnerabilities/exec` **and** `POST`                                   | Form submitted; payload unknown |


Dashboard / SOC workflow: treat **level ≥ 10** (or `rule.id:100016`) as the actionable CMDi alert. Do not escalate on 100010 alone.

### 4.2 Rule definition

See `rules/local_rules.xml` — rules **100016** and **100010**.

### 4.3 Thresholds and filters

- No frequency threshold
- Analyst filter: `rule.id:100016` or `rule.level:>=10`
- Optional: hide level ≤ 3 in default Threat Hunting views

---



## 5. Alert severity


| Rule   | Level | Justification                                               |
| ------ | ----- | ----------------------------------------------------------- |
| 100016 | 12    | Tokens in URI strongly indicate injection                   |
| 100010 | 3     | Benign ping and malicious POST look identical in access.log |


---



## 6. False positives


| Source                            | Rule   | Tuning                        |
| --------------------------------- | ------ | ----------------------------- |
| POST `8.8.8.8` (normal DVWA ping) | 100010 | Accept as level-3 telemetry   |
| GET exec form (no submit)         | —      | Should not fire 100010        |
| Lab demos with tokens in URL      | 100016 | Expected true positive in lab |


---



## 7. Response playbook

1. If **100016**: treat as likely CMDi — capture full `url`, src IP, timestamp
2. If **only 100010**: check Zeek HTTP for same window; do not assume injection
3. Pivot: `event.module: zeek and event.original: *vulnerabilities/exec*`
4. Confirm source (lab: Kali) and Scenario B operator log
5. Note Suricata may still be silent for POST-body CMDi

---



## 8. Test plan


| Test                                              | Expected                                        | Last result |
| ------------------------------------------------- | ----------------------------------------------- | ----------- |
| POST `8.8.8.8` only                               | **100010** (lvl 3); **no 100016**               | **Pass**    |
| POST `8.8.8.8; cat /etc/passwd`                   | **100010** (lvl 3); **no 100016** (body hidden) | **Pass**    |
| Request with exec + `;`/`whoami`/`%3B` in **URL** | **100016** (lvl 12)                             | **Pass**    |
| Browse DVWA home only                             | Neither rule                                    | **Pass**    |
| Open exec page GET (no POST)                      | Neither rule                                    | **Pass**    |


**Demo tip:** For a clean high-severity Wazuh screenshot, use a request where the payload appears in the query string so **100016** fires.

---



## 9. Metrics


| Metric                               | Target                    | Actual  |
| ------------------------------------ | ------------------------- | ------- |
| MTTD (100016)                        | < 1 min                   | < 1 min |
| FP rate on benign POST (as “attack”) | 0 if filtering level ≥ 10 |         |


---



## 10. References

- Scenario: `scenarios/B-dvwa-web/`
- Rules: `rules/local_rules.xml`
- Gap closed: No useful Wazuh signal on CMDi → split-confidence model (telemetry + URI-based high confidence)
- Residual gap: POST-body payloads still not visible to Wazuh without app/ModSecurity logging

