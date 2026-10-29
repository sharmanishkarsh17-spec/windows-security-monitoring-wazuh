# System Architecture

## Overview

The project implements a Windows security monitoring and threat-detection pipeline using Wazuh, Sysmon, Sigma, Filebeat, and OpenSearch.

The architecture is designed to collect endpoint telemetry, process security events, apply detection logic, and support investigation through indexed security data.

## Architecture Flow

Windows Endpoint
      |
      v
Wazuh Agent
      |
      v
Wazuh Manager
      |
      v
Filebeat
      |
      v
OpenSearch
      |
      v
Detection Investigation

## Windows Endpoint

The Windows endpoint is the primary telemetry source for the project.

### Windows Security Events

The project uses:

- Event ID 4625 — failed logon
- Event ID 4672 — special privileges assigned to a new logon
- Event ID 4688 — process creation

### Sysmon

The project uses:

- Event ID 10 — ProcessAccess
- Event ID 11 — FileCreate

The Sysmon configuration focuses on LSASS process-access monitoring and controlled file-creation telemetry.

## Wazuh Agent

The Wazuh Windows agent collects Windows Event Channel telemetry and forwards it to the Wazuh Manager.

Relevant event sources include:

- Windows Security
- Microsoft-Windows-Sysmon/Operational

## Wazuh Manager

The Wazuh Manager receives endpoint telemetry and performs event processing and rule evaluation.

Custom rule 100011 identifies Sysmon Event ID 11 file-creation events for the controlled validation pipeline.

## Filebeat

Filebeat forwards Wazuh data toward OpenSearch.

The repository contains a sanitized Filebeat configuration example. Credentials, certificates, keystores, and environment-specific values are intentionally excluded.

## OpenSearch

OpenSearch provides the indexed data layer used for investigation and validation.

Relevant fields include:

- Event ID
- Target user
- Source IP
- Process information
- Command-line information
- Target image
- Target filename
- Wazuh rule ID
- Privilege information

## Sigma Detection Layer

Sigma provides the platform-independent detection-rule layer.

The project contains five primary Sigma rules and one correlation rule.

The rules cover:

1. Failed authentication
2. Repeated failed authentication
3. Suspicious credential-access process telemetry
4. LSASS access
5. Credential-store-related file creation
6. Special-privilege logon telemetry

Sigma CLI was used to validate and convert the detection rules to OpenSearch-compatible query formats.

## Detection and Investigation Flow

The overall workflow is:

1. Windows generates security or Sysmon telemetry.
2. Wazuh Agent collects the telemetry.
3. Wazuh Manager processes the events and applies Wazuh rules.
4. Filebeat forwards Wazuh data to OpenSearch.
5. OpenSearch indexes the resulting event data.
6. Sigma detection logic provides the detection-engineering layer.
7. Security events are queried and investigated.
8. Validation results are recorded for each detection.

## Detection Engineering Workflow

The detection engineering process is:

Identify Telemetry
       |
       v
Define Detection Logic
       |
       v
Write Sigma Rule
       |
       v
Validate Sigma Syntax
       |
       v
Convert to OpenSearch Query
       |
       v
Generate or Observe Controlled Telemetry
       |
       v
Verify Wazuh Collection
       |
       v
Investigate in OpenSearch
       |
       v
Record Validation Result

## Security Design Considerations

The architecture separates:

- Endpoint telemetry collection
- Event processing
- Detection logic
- Alert generation
- Data indexing
- Security investigation

This separation makes the detection logic easier to maintain and allows Sigma rules to be converted into different query environments.

## Validation Scope

The project was validated using controlled activity and naturally occurring Windows telemetry.

Validation included:

- Windows failed-logon telemetry
- Controlled process-creation telemetry for credential-access keyword detection
- Sysmon LSASS process-access telemetry
- Controlled Sysmon FileCreate telemetry
- Windows special-privilege telemetry
- Sigma rule syntax and conversion validation

No actual credential-dumping operation was executed.

## Environment Security

The public repository intentionally excludes sensitive or unnecessary environment-specific information.

The repository does not include:

- Passwords
- API keys
- Private keys
- Filebeat keystores
- Private certificates
- Raw OpenSearch databases
- Raw security logs
- Environment-specific credentials

Sanitized .example configuration files are provided where configuration structure is useful.

## Repository References

Implementation files:

- sigma/rules.yml
- sysmon/sysmon-config.xml
- wazuh/custom-rules/local_rules.xml
- wazuh/agent-config/ossec.conf.example
- wazuh/filebeat/filebeat.yml.example

Validation documentation:

- validation/test_plan.md
- validation/validation_matrix.md
- validation/sample-queries.md
- docs/validation-results.md
