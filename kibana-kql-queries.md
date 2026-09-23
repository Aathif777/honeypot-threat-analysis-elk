# Kibana KQL Queries

This file documents representative KQL queries used in the project.

> These queries are based on the queries and investigation descriptions documented in the project report.

## 1. SSH Traffic

```kql
DestPort: 22
```

Used to isolate SSH-targeted events.

## 2. SSH Traffic from Russia

```kql
DestPort: 22 and Country: RU
```

Used to narrow the SSH dataset by source country.

## 3. Repeat-Offender IP

```kql
src_ip: "109.68.191.194"
```

Used to investigate a high-volume source IP.

## 4. Cowrie Download Behaviour

```kql
type: "Cowrie" and input: *wget*
```

Used to identify Cowrie command events containing `wget`.

## 5. Exact IoC / Command Investigation

The report also used an exact-value filter against a Cowrie command event to isolate an individual intrusion sequence.

For a public repository, keep exact indicators documented as dataset-derived IoCs and avoid presenting them as your own infrastructure.

## Analysis Workflow

The queries were used as part of the following workflow:

1. Filter the event corpus.
2. Review field statistics.
3. Expand individual documents.
4. Correlate IP, port, country, ASN and signature data.
5. Validate findings.
6. Build dashboard visualisations.
