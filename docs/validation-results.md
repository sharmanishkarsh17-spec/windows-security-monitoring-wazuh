# Validation Results

## Overview

The detection pipeline was validated using Windows Security Events, Sysmon telemetry, Wazuh alerting, and OpenSearch investigation.

The validation focused on confirming that required telemetry was generated, collected by Wazuh, indexed for investigation, and associated with the intended detection logic.

## Validation Summary

| Detection | Telemetry | Validation Status | Evidence |
|---|---|---|---|
| Windows Failed Logon | Event ID 4625 | Validated | Failed-logon telemetry observed in Wazuh/OpenSearch |
| Repeated Failed Logons from Single Source | Sigma correlation | Logic Validated | Correlation converted successfully; configured threshold not positively observed |
| Suspicious Credential Dumping Tool Execution | Event ID 4688 | Safely Validated | Controlled keyword-based process telemetry |
| Unusual LSASS Memory Access | Sysmon Event ID 10 | Validated | LSASS-access telemetry and Wazuh alert observed |
| Browser and Vault Credential Store Access | Sysmon Event ID 11 | Pipeline Validated | Controlled FileCreate telemetry reached Wazuh custom rule |
| Special Privileges Assigned to Non-System User | Event ID 4672 | Validated | Special-privilege telemetry observed in Wazuh/OpenSearch |

## 1. Windows Failed Logon

**Event:** Windows Security Event ID 4625

Failed-logon telemetry was observed in OpenSearch and associated with Wazuh alerting.

The observed telemetry included:

- Event ID 4625
- Target user information
- Source IP information
- Wazuh rule association

The Sigma rule excludes expected system/service accounts from the primary detection logic.

**Status:** Validated.

## 2. Repeated Failed Logons from Single Source

This detection uses Sigma correlation logic to identify more than five matching failed-logon events from the same source IP within five minutes.

The correlation rule was successfully validated for syntax and conversion to an OpenSearch-compatible query.

The configured threshold was not positively observed during the available testing.

**Status:** Logic Validated.

## 3. Suspicious Credential Dumping Tool Execution

**Event:** Windows Security Event ID 4688

The detection monitors process-creation command-line telemetry for credential-access-related keywords.

Validation used controlled process telemetry rather than executing an actual credential-dumping operation.

The test demonstrated that the detection concept can be evaluated through Windows process-creation telemetry.

**Status:** Safely Validated.

## 4. Unusual LSASS Memory Access

**Event:** Sysmon Event ID 10

Sysmon process-access telemetry targeting `lsass.exe` was observed.

The Wazuh environment generated alerts for LSASS access telemetry. Observed activity included legitimate Windows Defender process access to LSASS, demonstrating why detection results require investigation and contextual analysis.

**Status:** Validated.

## 5. Browser and Vault Credential Store Access

**Event:** Sysmon Event ID 11

The detection monitors FileCreate telemetry associated with browser and Windows credential-store locations.

For pipeline validation, controlled file creation under the configured Sysmon validation directory was performed.

The resulting Sysmon Event ID 11 telemetry reached Wazuh and triggered the custom Wazuh rule `100011`.

**Status:** Pipeline Validated.

## 6. Special Privileges Assigned to Non-System User

**Event:** Windows Security Event ID 4672

Windows special-privilege logon telemetry was observed in OpenSearch.

The observed events included privilege information such as:

- SeDebugPrivilege
- SeBackupPrivilege
- SeRestorePrivilege
- SeImpersonatePrivilege
- SeTakeOwnershipPrivilege

This telemetry demonstrates the availability of privilege-related Windows security events for detection and investigation.

**Status:** Validated.

## Sigma Validation

The Sigma rules were checked using Sigma CLI.

The project environment used:

- Sigma CLI 3.1.0
- OpenSearch-compatible Sigma conversion targets

The rules successfully converted to OpenSearch-compatible query formats.

## Overall Result

The project successfully demonstrated an end-to-end security monitoring and detection workflow:

Windows Endpoint
→ Wazuh Agent
→ Wazuh Manager
→ Filebeat
→ OpenSearch
→ Detection Investigation

The validation demonstrates practical telemetry collection, detection-rule development, Sigma conversion, Wazuh alerting, and OpenSearch-based investigation.

Testing was performed using controlled activity where appropriate, and no actual credential-dumping operation was executed.
