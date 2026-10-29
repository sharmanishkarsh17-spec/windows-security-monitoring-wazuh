# Windows Security Monitoring & Threat Detection Using Sigma and Wazuh

## Overview

This project implements a Windows security monitoring and threat detection pipeline using Wazuh, Sysmon, Sigma, OpenSearch, and Windows Security Event Logs.

The project focuses on collecting Windows endpoint telemetry, developing Sigma-based detections, validating detections safely in a controlled lab environment, and investigating the resulting alerts through Wazuh and OpenSearch.

## Architecture

Windows Endpoint → Wazuh Agent → Wazuh Manager → Filebeat → OpenSearch → Wazuh Dashboard

## Technologies

- Wazuh 4.14.7
- Sysmon
- Sigma CLI 3.1.0
- OpenSearch
- Filebeat
- Windows Security Event Logs
- PowerShell
- Linux / WSL
- MITRE ATT&CK

## Detection Coverage

| Detection | Event | Purpose | MITRE ATT&CK |
|---|---:|---|---|
| Windows Failed Logon | 4625 | Detect failed authentication attempts | T1110 |
| Repeated Failed Logons from Single Source | Correlation | Identify repeated failed logons from the same source | T1110 |
| Suspicious Credential Dumping Tool Execution | 4688 | Detect suspicious credential-access keywords in process creation telemetry | T1110 |
| Unusual LSASS Memory Access | Sysmon 10 | Monitor process access to LSASS | T1003.001 |
| Browser and Vault Credential Store Access | Sysmon 11 | Detect file-creation activity associated with credential-store locations | T1555 |
| Special Privileges Assigned to Non-System User | 4672 | Monitor special-privilege logon telemetry | T1548 |

## Validation

The detections were validated against telemetry generated in the Wazuh/Sysmon lab environment.

- Windows Event ID 4625 failed-logon telemetry validated
- Event ID 4688 process-creation telemetry validated
- Sysmon Event ID 10 LSASS-access telemetry validated
- Sysmon Event ID 11 FileCreate telemetry validated through a custom Wazuh rule
- Windows Event ID 4672 privileged-logon telemetry validated
- Sigma rules checked with Sigma CLI
- Sigma rules converted to OpenSearch-compatible query formats

Credential-dumping behavior was validated safely using controlled keyword-based process telemetry; no actual credential-dumping attack was executed.

## Repository Structure

    windows-security-monitoring-wazuh/
    ├── README.md
    ├── docs/
    │   ├── architecture.md
    │   ├── detection-methodology.md
    │   ├── validation-results.md
    │   ├── live-demo-guide.md
    │   └── limitations.md
    ├── sigma/
    │   ├── rules.yml
    │   └── README.md
    ├── wazuh/
    │   ├── custom-rules/
    │   ├── agent-config/
    │   └── filebeat/
    ├── sysmon/
    ├── validation/
    ├── evidence/
    ├── SECURITY.md
    ├── LICENSE
    └── .gitignore

## Project Highlights

- Built and configured the Wazuh, Sysmon, Filebeat, and OpenSearch monitoring environment
- Developed five primary Sigma detection rules and one correlation rule
- Mapped detections to MITRE ATT&CK techniques
- Validated detections using real Windows telemetry and controlled test activity
- Documented detection methodology, validation results, limitations, and live demonstration steps

## Scope and Limitations

This is a controlled individual lab project intended for security monitoring and detection engineering practice. It is not intended to represent a production-scale SOC deployment.

Some detections were validated using safe substitutes or controlled telemetry rather than executing real credential-theft techniques.

## Documentation

- docs/architecture.md — monitoring architecture
- docs/detection-methodology.md — detection methodology
- docs/validation-results.md — validation results
- docs/live-demo-guide.md — demonstration procedure
- docs/limitations.md — project limitations
- evidence/validation-summary.md — validation evidence summary

## Security

See SECURITY.md for security and responsible-disclosure guidance.

## License

This project is released under the MIT License.
