# Baseline Snapshot

**Collected:** *09/01/2026*  
**Window:** 24 hours  
**Status:** **Complete**

---

## Wazuh agent health


| Agent name     | Host           | IP          | Status     |
| -------------- | -------------- | ----------- | ---------- | 
| DVWA           | DVWA           | 192.168.4.2 | **Active** |
| Metasploitable | Metasploitable | 192.168.4.3 | **Active** |


---

## NSM → ELK Ingestion


| Index / data view | Sample query                                   | Documents (24h) | Healthy? |
| ----------------- | ---------------------------------------------- | --------------- | -------- |
| Zeek Connection   | `event.module: zeek and event.dataset: *conn*` | 267,711         | **Yes**  |
| Suricata          | `event.module: suricata`                       | 12,351          | **Yes**  |
| Snort             | `log_type: snort`                              | 41,639          | **Yes**  |


---



## Baseline Volumes 


| Source   | Saved Search              | Documents (24h) | Primary Noise       | Screenshot |
| -------- | ------------------------- | --------------- | ------------------- | ---------- |
| Zeek     | `baseline-zeek-24hrs`     | 267,711         | Connection metadata |            |
| Suricata | `baseline-suricata-24hrs` | 12,351          | Stats/alerts        |            |
| Snort    | `baseline-snort-24hrs`    | 41,639          | Signature alerts    |            |


---



## Baseline Description

*Steady Zeek connection records (aprox. 5,300) with spikes during scans. Suricata records displayed about every 8s. Snort alerts are consistently between 300-500 with large spikes during scans.*

---
