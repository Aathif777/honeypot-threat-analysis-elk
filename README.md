# Honeypot Threat Analysis with the ELK Stack

> Cyber Security: Incident Detection and Response — project report and threat-analysis portfolio project.

## Overview

This project analyses one week of attack telemetry collected by **T-Pot**, an open-source multi-honeypot platform, using the **Elastic Stack (Elasticsearch, Logstash and Kibana)**.

The dataset contains approximately **10.3 million honeypot events** from a restored November 2024 T-Pot snapshot. The analysis was performed in Kibana Discover and through three purpose-built Kibana dashboards.

The project focuses on turning raw honeypot telemetry into actionable threat intelligence through filtering, profiling, investigation, correlation, visualization, and MITRE ATT&CK mapping.

> **Data scope:** This project analyses a restored static dataset. It does not claim live ingestion or a live production honeypot deployment.

## Technologies

- T-Pot multi-honeypot platform
- Elasticsearch
- Kibana
- Logstash / Elastic Stack
- Docker
- Microsoft Azure Virtual Machine
- Kibana Discover
- Kibana Lens and Maps
- Suricata
- Cowrie
- MITRE ATT&CK
- KQL

## Environment

The Elastic environment was deployed using the `sebp/elk` Docker image inside Docker Desktop on a Microsoft Azure virtual machine.

The Elasticsearch data was persisted using a Docker named volume so that the restored indices survived container restarts.

The project analysed a restored `logstash-*` data view containing approximately 10.3 million documents.

## Investigation Methodology

The investigation followed a layered workflow similar to a SOC analysis process:

1. **Filter** — narrow the dataset using field-value filters and KQL.
2. **Profile** — inspect field statistics and distributions.
3. **Expand** — examine individual documents and raw event context.
4. **Correlate** — combine indicators such as port, country, ASN and alert signature.
5. **Visualise** — turn validated findings into dashboards for communication.

## Investigations

### 1. Service Targeting and Geographic Distribution

The investigation examined traffic targeting SSH on destination port 22.

The SSH subset contained approximately 500,335 events. The leading source countries in that subset were Russia, India, Germany, the United States and China.

The analysis demonstrates how a large event corpus can be progressively narrowed using destination port and geographic filters.

### 2. Repeat Offender Investigation

A single source IP, `109.68.191.194`, generated **77,351 events** during the seven-day dataset period.

The investigation correlated:

- Source IP
- Attacker-side GeoIP information
- IP reputation
- SSH client banner
- Suricata metadata

The report identifies the observed traffic as consistent with automated activity.

### 3. Cowrie Payload Download Behaviour

Cowrie command events containing `wget` were investigated to identify download behaviour.

The analysis identified examples involving:

- `busybox` / `tftp` command chains
- Remote script downloads
- Mirai-style activity
- Systemd-based persistence commands

### 4. End-to-End IoC Investigation

A single Cowrie command was investigated from source attribution through the observed command chain.

The analysis identified actionable indicators including:

- Source IP
- Remote URL
- Destination port
- Downloaded filename
- Command sequence

### 5. Suricata Network Alert Analysis

Suricata telemetry was investigated to identify network-layer activity.

The analysis correlated source geography, ASN information and Suricata signatures and identified DNS amplification-related activity in the dataset.

## Kibana Dashboards

Three dashboards were created.

### Attack Origin Intelligence

Answers: **Where are attacks coming from?**

Includes:

- Attacker geographic map
- Source-country ranking
- ASN distribution
- Source-IP persistence over time

### Honeypot Activity Trends

Answers: **When is the honeypot being attacked?**

Includes:

- Attack volume over time
- Honeypot-type breakdown
- Hour-of-day activity
- Unique source IP trends

### Attack Techniques & Payloads

Answers: **What are attackers attempting to do?**

Includes:

- Destination-port activity
- Cowrie command analysis
- Event-type funnel
- Suricata signature rankings

## Key Findings

- Attack activity in the dataset was heavily automated.
- SSH was one of the heavily probed services.
- A small number of source IPs generated a large amount of telemetry.
- Cowrie data contained automated download-and-execute behaviour.
- The analysis identified defence-evasion and persistence behaviours.
- Legacy services and vulnerabilities continued to appear in the observed telemetry.
- Cloud and hosting providers appeared repeatedly in source ASN analysis.
- Country-level blocking alone would not adequately describe or address the observed activity.

## MITRE ATT&CK Mapping

| Observed behaviour | MITRE ATT&CK technique |
|---|---|
| `uname -a`, `/proc/cpuinfo`, `free -m` | T1082 — System Information Discovery |
| `wget` / `tftp` downloads | T1105 — Ingress Tool Transfer |
| `chmod 777` and payload execution | T1059 — Command and Scripting Interpreter |
| `lockr` / `chattr` against `.ssh` | T1562.001 — Impair Defenses |
| Systemd service with `ExecStart` script | T1543.002 — Create or Modify System Process |
| DoublePulsar / VNC / DNS amplification activity | T1210 / T1046 / T1498 |

## Defensive Recommendations

The report recommends:

- Harden SSH using key-based authentication and appropriate rate limiting.
- Patch or isolate legacy services.
- Restrict or retire exposed legacy protocols such as Telnet and VNC where appropriate.
- Operationalise observed IoCs in SIEM correlation and blocking workflows.
- Segment IoT/OT environments and apply appropriate egress controls.
- Apply DNS security controls and avoid open resolvers.
- Use IP reputation and ASN-level threat intelligence where appropriate.

## Challenges and Lessons Learned

The project highlighted several practical issues:

- **Attribution accuracy:** attacker GeoIP data had to be distinguished from T-Pot's own external-interface GeoIP information.
- **Field mappings:** exact filtering and aggregation required understanding Elasticsearch `.keyword` fields.
- **Sampling:** Kibana field statistics were checked against full queries before reporting headline percentages.
- **Schema differences:** different honeypots generated different fields, requiring investigation by honeypot type.
- **SOC workflow:** the filter → profile → expand → correlate process closely resembles practical security triage.

## Repository Structure

```text
honeypot-threat-analysis-elk/
├── README.md
├── report/
│   └── Honeypot-Threat-Analysis.pdf
├── queries/
│   └── kibana-kql-queries.md
├── docs/
│   └── methodology.md
├── dashboards/
│   └── README.md
└── screenshots/
    └── README.md
```

## Project Type

**Academic / Portfolio Cybersecurity Project**

This repository documents analysis performed on a restored T-Pot telemetry dataset. The results should not be interpreted as live monitoring of a production environment.

## References

- Elastic — Kibana documentation
- MITRE ATT&CK
- Nawrocki et al. — A survey on honeypot software and data analysis
- Spitzner — *Honeypots: Tracking Hackers*
- Telekom Security — T-Pot
