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
| Zeek     | `baseline-zeek-24hrs`     | 267,711         | Connection metadata | [Image](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Project%20Components/Baseline/Screenshots/zeek-baseline-24hrs.png)           |
| Suricata | `baseline-suricata-24hrs` | 12,351          | Stats/alerts        | [Image](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Project%20Components/Baseline/Screenshots/baseline-suricata-24hrs.png)           |
| Snort    | `baseline-snort-24hrs`    | 41,639          | Signature alerts    | [Image](https://github.com/JonahCaro1/ELK-Wazuh-Detection/blob/main/Project%20Components/Baseline/Screenshots/baseline-snort-24hrs.png)           |


---



## Baseline Description

*Steady Zeek connection records (aprox. 5,300) with spikes during scans. Suricata records displayed about every 8s. Snort alerts are consistently between 300-500 with large spikes during scans.*

---
