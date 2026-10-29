# Live Demo Guide

## Purpose

This guide provides a repeatable sequence for demonstrating the Windows Security Monitoring & Threat Detection project.

The demonstration focuses on telemetry collection, detection logic, Wazuh alerting, OpenSearch investigation, and Sigma-based detection engineering.

## Demo Architecture

Windows Endpoint → Wazuh Agent → Wazuh Manager → Filebeat → OpenSearch

## Recommended Demo Order

1. Show the project architecture.
2. Show the Sigma detection rules.
3. Show the Windows/Sysmon telemetry sources.
4. Generate or identify controlled validation telemetry.
5. Show the resulting Wazuh alert.
6. Open the corresponding event in OpenSearch.
7. Explain the relevant event fields.
8. Show the associated Sigma rule.
9. Explain the MITRE ATT&CK mapping.
10. Show the validation status.

## Demo 1 — Failed Logon Detection

### What to Show

- Windows Security Event ID 4625
- Wazuh alert associated with the failed logon
- OpenSearch event details
- Sigma failed-logon rule

### Explanation

Explain that the detection identifies failed authentication attempts while excluding expected system and service accounts.

## Demo 2 — Process Creation Detection

### What to Show

- Windows Security Event ID 4688
- Process creation telemetry
- Corresponding Sigma detection rule
- OpenSearch event details

### Explanation

Explain that the rule monitors process-creation command-line telemetry for credential-access-related keywords.

Validation used controlled keyword-based process telemetry. No actual credential-dumping operation was executed.

## Demo 3 — LSASS Access Detection

### What to Show

- Sysmon Event ID 10
- Target image showing lsass.exe
- Wazuh alert
- OpenSearch event
- Sigma LSASS detection

### Explanation

Explain that Sysmon Event ID 10 provides process-access telemetry and the detection focuses on access targeting LSASS.

Observed activity can include legitimate security software access, so alert investigation requires contextual analysis.

## Demo 4 — FileCreate Detection

### What to Show

- Sysmon Event ID 11
- Controlled file-creation event
- Wazuh custom rule 100011
- OpenSearch event
- Sigma FileCreate detection

### Explanation

Explain that Sysmon Event ID 11 records file-creation activity. The project uses controlled file creation to validate the telemetry pipeline.

The validation demonstrated that the event reached Wazuh and triggered custom rule 100011.

## Demo 5 — Special Privilege Telemetry

### What to Show

- Windows Security Event ID 4672
- Privilege information
- Wazuh/OpenSearch event
- Corresponding Sigma detection

### Explanation

Explain that Event ID 4672 records special privileges assigned to a new logon and can provide useful privilege-related telemetry for investigation.

## Demo 6 — Sigma Conversion

### What to Show

From the Sigma environment, demonstrate:

sigma check rules.yml
sigma list targets
sigma convert -t open_search_ppl rules.yml

### Explanation

Explain that Sigma provides platform-independent detection logic and the rules can be converted into OpenSearch-compatible query formats.

## Key Questions a Recruiter May Ask

### Why Sigma?

Sigma provides a structured, platform-independent format for describing security detections and makes detection logic easier to reuse across supported platforms.

### Why Wazuh?

Wazuh provides endpoint monitoring, event processing, rule evaluation, and alerting capabilities for the Windows telemetry collected in the project.

### Why Sysmon?

Sysmon provides additional Windows endpoint telemetry that is useful for process access and file-creation monitoring.

### Why OpenSearch?

OpenSearch provides indexed security-event data that can be queried during investigation and validation.

### Did you execute Mimikatz?

No. The project used controlled keyword-based process telemetry to validate the detection concept. No actual credential-dumping operation was executed.

### Was the correlation threshold triggered?

The Sigma correlation logic was successfully converted and validated, but the configured threshold was not positively observed during testing.

### What did you actually build?

The project combines Windows Security Events, Sysmon telemetry, Wazuh collection and alerting, Sigma detection logic, Filebeat forwarding, and OpenSearch-based investigation into an end-to-end security monitoring workflow.

## Demo Preparation Checklist

- Wazuh Manager running
- Wazuh Agent connected
- Sysmon running
- OpenSearch available
- Wazuh Dashboard available
- Sigma virtual environment available
- Project repository available
- Relevant validation telemetry available

## Demo Safety

Use controlled validation activity during demonstrations. Do not execute real credential-dumping or destructive activity merely to demonstrate the detection pipeline.
