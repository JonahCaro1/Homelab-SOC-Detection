# Detection Use Case: PwnKit Privilege Escalation


| Field                | Value                                                            |
| -------------------- | ---------------------------------------------------------------- |
| **Use Case ID**      | UC-005                                                           |
| **Title**            | PwnKit Privilege Escalation (pkexec / CVE-2021-4034)             |
| **Author**           | Jonah Caro                                                       |
| **Version**          | 1.1                                                              |
| **Status**           | **Production (Lab)**                                             |
| **Last Tested**      | 09/19/2026                                                       |
| **Related Scenario** | Post-Exploitation: Privilege Escalation                          |
| **Wazuh Rule IDs**   | **100020** (Primary, level 12) · **100021** (Telemetry, level 6) |


---



## Objective

Close the Post-Exploitation scenario's **host detection gap** for local privilege escalation via **PwnKit** (`pkexec` / CVE-2021-4034). NSM (Zeek / Suricata) correctly does not see this technique as it is not network-borne. Detection depends on **auditd** telemetry of `pkexec` execution on the Wazuh agent host.

- **High confidence (100020):** `audit.execve.argc` = `0` (classic PwnKit argv behavior)
- **Telemetry (100021):** `audit.key` = `pkexec_exec` from the audit watch on `/usr/bin/pkexec`

**Prerequisite:** `auditd` installed and loaded on the target, agent collecting `/var/log/audit/audit.log`.

---



## MITRE ATT&CK


| Tactic               | Technique                             | ID    |
| -------------------- | ------------------------------------- | ----- |
| Privilege Escalation | Exploitation for Privilege Escalation | T1068 |
| Privilege Escalation | Abuse Elevation Control Mechanism     | T1548 |


---



## Data sources


| Source   | Log type                      | Index / location             |
| -------- | ----------------------------- | ---------------------------- |
| auditd   | `execve` on `/usr/bin/pkexec` | Target agent → Wazuh manager |
| Wazuh    | Custom rules 100020 / 100021  | Wazuh Dashboard              |
| Zeek     | N/A (local LPE)               | N/A                          |
| Suricata | N/A (local LPE)               | N/A                          |


---



## Logic


| Rule       | Level | Condition                                     | Meaning                                   |
| ---------- | ----- | --------------------------------------------- | ----------------------------------------- |
| **100020** | 12    | Parent **80700**; `audit.execve.argc` = `0`   | PwnKit-style EXECVE argv (actionable)     |
| **100021** | 6     | Parent **80700**; `audit.key` = `pkexec_exec` | pkexec executed (telemetry / correlation) |




### Rule Definition

See `rules/local_rules.xml`: Rules **100020** and **100021**

Example Audit Rule (`/etc/audit/rules.d/pkexec.rules`):

```text
-a always,exit -F arch=b64 -S execve -F path=/usr/bin/pkexec -F key=pkexec_exec
-a always,exit -F arch=b32 -S execve -F path=/usr/bin/pkexec -F key=pkexec_exec
```

---



## Alert Severity


| Rule   | Level | Justification                                     |
| ------ | ----- | ------------------------------------------------- |
| 100020 | 12    | High-confidence PwnKit-style `pkexec` with argc=0 |
| 100021 | 6     | Any watched `pkexec` — may include admin use      |


---



## False Positives


| Source                        | Rule   | Tuning                                           |
| ----------------------------- | ------ | ------------------------------------------------ |
| Legitimate `pkexec` by admins | 100021 | Whitelist users/hosts; prefer 100020             |
| Package managers / Polkit UI  | 100021 | Tune 100021, keep 100020 (argc=0) as high signal |
| Missing Auditd                | Both   | No alert (blind), document as control gap        |


---



## Response playbook

1. If **100020**: treat as likely privilege escalation. Capture host, user, timestamp, `pkexec` audit fields.
2. Check whether effective UID became **0** after the event.
3. Pivot: recent downloads (`wget`/`curl`) in Zeek/Suricata that may have staged the exploit.
4. Containment: isolate agent host, patch Polkit, restrict `pkexec`, rebuild if compromised.
5. If **only 100021**: verify argc/cmdline before escalating.

---



## Testing


| Test                                                  | Expected                                | Last result |
| ----------------------------------------------------- | --------------------------------------- | ----------- |
| auditd loaded; `ausearch -k pkexec_exec` shows events | Audit path healthy                      | **Pass**    |
| Controlled PwnKit / `pkexec` with argc=0              | **100020** in Wazuh                     | **Pass**    |
| Benign `pkexec --help`                                | **100021** (not 100020)                 | **Pass**    |
| Exploit with auditd **disabled**                      | No Wazuh LPE alert (documents host gap) | **Pass**    |


---



## Metrics


| Metric        | Target                                             | Actual                                                 |
| ------------- | -------------------------------------------------- | ------------------------------------------------------ |
| MTTD (100020) | < 1 min                                            | < 1 min                                                |
| Coverage      | Wazuh detects privilege escalation that NSM cannot | Wazuh detected privilege escalation on the target host |


---



## References

- Scenario: `scenarios/C-metasploitable/` (privilege escalation / PwnKit)
- Rules: `rules/local_rules.xml`
- CVE: [CVE-2021-4034](https://nvd.nist.gov/vuln/detail/CVE-2021-4034) (PwnKit)
- Gap Closed: Privilege escalation previously undetected by Wazuh, NSM remains correctly blind to local-only technique
- Residual Gap: Without auditd on the agent host, both rules are blind

