# Detection Methodology

## Overview

The project implements a Windows security monitoring and threat-detection workflow using Wazuh, Sysmon, Sigma, Filebeat, and OpenSearch.

The detection methodology follows this flow:

Windows Endpoint
→ Windows Security / Sysmon Telemetry
→ Wazuh Agent
→ Wazuh Manager
→ Filebeat
→ OpenSearch
→ Detection Investigation

Sigma rules are used to define detection logic in a platform-independent format and are converted to OpenSearch-compatible query formats for analysis.

## Telemetry Sources

### Windows Security Events

Windows Security Event Logs provide authentication and privilege-related telemetry.

The project uses:

- Event ID 4625 — failed logon
- Event ID 4672 — special privileges assigned to a new logon
- Event ID 4688 — process creation

### Sysmon

Sysmon provides additional endpoint telemetry.

The project uses:

- Event ID 10 — ProcessAccess
- Event ID 11 — FileCreate

The Sysmon configuration focuses on LSASS process-access monitoring and controlled file-creation telemetry.

## Detection Logic

The Sigma rules cover:

1. Failed authentication attempts
2. Repeated failed authentication attempts from a single source
3. Suspicious credential-access keywords in process creation telemetry
4. Process access to LSASS
5. File creation associated with credential-store locations
6. Special-privilege logon telemetry for non-system users

## Detection Engineering Workflow

The detection development workflow was:

1. Identify the Windows or Sysmon telemetry required for a detection.
2. Define the detection logic using Sigma.
3. Add appropriate exclusions to reduce expected system activity.
4. Map the detection to a relevant MITRE ATT&CK technique.
5. Validate the Sigma syntax.
6. Convert the rule to an OpenSearch-compatible query.
7. Generate controlled telemetry where appropriate.
8. Verify that the resulting telemetry reaches Wazuh and OpenSearch.
9. Investigate the resulting event fields and Wazuh alert data.
10. Record the validation status and limitations.

## Validation Approach

Testing was performed using controlled activity in the Windows environment.

Examples included:

- Controlled failed authentication activity
- Controlled process-creation telemetry containing detection keywords
- Observation of Sysmon LSASS access telemetry
- Controlled file creation under the Sysmon validation path
- Observation of Windows special-privilege logon telemetry

No actual credential-dumping operation was executed.

## Correlation Detection

The repeated-failed-logon detection uses Sigma correlation logic to group failed-logon events by source IP address within a defined time window.

The configured condition is more than five matching events within five minutes.

The correlation rule was successfully converted and its logic was validated. The configured threshold was not positively observed during the available testing.

## Tooling

Primary tools used:

- Wazuh 4.14.7
- Sigma CLI 3.1.0
- Sysmon
- Filebeat
- OpenSearch
- Windows Event Logs
- PowerShell
- Linux/WSL

## Objective

The objective is to demonstrate practical detection engineering:

- Collect endpoint telemetry
- Normalize and analyze security events
- Develop reusable Sigma detections
- Map detections to MITRE ATT&CK
- Validate detections against real or controlled telemetry
- Investigate alerts through Wazuh and OpenSearch
