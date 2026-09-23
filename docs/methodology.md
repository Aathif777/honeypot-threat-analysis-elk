# Methodology

## Dataset

The project uses one week of T-Pot telemetry from November 2024. The dataset was restored from a snapshot into Elasticsearch and analysed through the `logstash-*` data view.

No live ingestion was performed for this analysis.

## Investigation Workflow

### Filter

Use KQL and Kibana filter pills to reduce the event corpus to a specific question.

### Profile

Inspect distributions and field statistics to identify potentially significant values.

### Expand

Open individual documents to inspect raw event fields and surrounding context.

### Correlate

Combine multiple dimensions such as:

- Destination port
- Source IP
- Country
- ASN
- Honeypot type
- Suricata signature
- Command input

### Visualise

Create dashboard panels from validated findings to communicate:

- Attack origin
- Attack timing
- Honeypot activity
- Attacker commands
- Network signatures
- Payload behaviour

## Important Data Considerations

The dataset contains separate GeoIP information for the attacker source and the T-Pot external interface. These fields must not be confused during attribution.

Kibana field statistics can operate on samples, so headline percentages should be validated using full queries where appropriate.

Different honeypot sensors produce different schemas, so investigations should be scoped to relevant event types and fields.
