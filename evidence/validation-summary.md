# Validation Summary

## Purpose

This document summarizes the validation evidence collected for the Windows Security Monitoring & Threat Detection project.

The validation confirms telemetry collection, Wazuh processing, OpenSearch indexing, Sigma detection logic, and controlled detection testing across the project environment.

## Validation Matrix

| Detection | Source | Event | Result | Evidence |
|---|---|---:|---|---|
| Windows Failed Logon | Windows Security | 4625 | Validated | Failed-logon telemetry observed in Wazuh/OpenSearch |
| Repeated Failed Logons | Sigma Correlation | Correlation | Logic Validated | Conversion and threshold logic validated; threshold not positively observed |
| Suspicious Credential-Access Process | Windows Security | 4688 | Safely Validated | Controlled keyword-based process telemetry |
| Unusual LSASS Access | Sysmon | 10 | Validated | LSASS-access telemetry and Wazuh alert observed |
| Credential-Store FileCreate | Sysmon | 11 | Pipeline Validated | Controlled FileCreate telemetry triggered Wazuh rule 100011 |
| Special Privilege Telemetry | Windows Security | 4672 | Validated | Privilege-related telemetry observed in Wazuh/OpenSearch |

## 1. Windows Failed Logon

Event ID 4625 telemetry was observed in OpenSearch and associated with Wazuh alerting.

Observed information included target user details, source IP information, and Wazuh rule association.

Status: Validated.

## 2. Repeated Failed Logons

The Sigma correlation detection groups failed-logon events by source IP address within a five-minute window and identifies more than five matching events.

The correlation rule successfully converted to an OpenSearch-compatible query format.

The configured threshold was not positively observed during testing.

Status: Logic Validated.

## 3. Suspicious Credential-Access Process

Windows Event ID 4688 process-creation telemetry was used to evaluate the credential-access keyword detection.

Validation used controlled process telemetry rather than executing an actual credential-dumping operation.

Status: Safely Validated.

## 4. Unusual LSASS Access

Sysmon Event ID 10 telemetry targeting lsass.exe was observed in the environment.

Wazuh generated alerts for LSASS access telemetry. Observed activity included legitimate Windows Defender process access to LSASS, demonstrating the importance of contextual investigation.

Status: Validated.

## 5. Credential-Store FileCreate

Sysmon Event ID 11 was used to validate the FileCreate telemetry pipeline.

Controlled file creation under the configured validation directory generated Sysmon telemetry that reached Wazuh and triggered custom rule 100011.

The validation confirms the telemetry and alerting pipeline rather than access to real browser credential databases or Windows credential stores.

Status: Pipeline Validated.

## 6. Special Privilege Telemetry

Windows Event ID 4672 telemetry was observed in OpenSearch.

Observed privilege information included SeDebugPrivilege, SeBackupPrivilege, SeRestorePrivilege, SeImpersonatePrivilege, and SeTakeOwnershipPrivilege.

Status: Validated.

## Sigma Validation

The Sigma rules were checked using Sigma CLI 3.1.0.

The rules were successfully converted to OpenSearch-compatible query formats using the available Sigma conversion targets.

## Overall Result

The project demonstrated an end-to-end security monitoring and detection-engineering workflow:

Windows Endpoint → Wazuh Agent → Wazuh Manager → Filebeat → OpenSearch

The validation demonstrates practical endpoint telemetry collection, detection-rule development, Sigma conversion, Wazuh alerting, and OpenSearch-based investigation.

Testing used controlled activity where appropriate, and no actual credential-dumping operation was executed.
