# Sigma Detection Rules

This directory contains Sigma detection rules developed for the Windows Security Monitoring & Threat Detection project.

## Detection Coverage

The project contains five primary Sigma detection rules and one correlation rule covering authentication failures, credential-access activity, LSASS access, credential-store file creation, and privileged logon telemetry.

| Detection | Windows / Sysmon Event | Purpose | MITRE ATT&CK |
|---|---:|---|---|
| Windows Failed Logon | 4625 | Detect failed authentication attempts | T1110 |
| Repeated Failed Logons from Single Source | Correlation | Identify repeated failed logons from the same source | T1110 |
| Suspicious Credential Dumping Tool Execution | 4688 | Detect suspicious credential-access keywords in process creation telemetry | T1110 |
| Unusual LSASS Memory Access | Sysmon 10 | Monitor process access to LSASS | T1003.001 |
| Browser and Vault Credential Store Access | Sysmon 11 | Detect file-creation activity associated with credential-store locations | T1555 |
| Special Privileges Assigned to Non-System User | 4672 | Monitor special-privilege logon telemetry for non-system users | T1548 |

## Rule Validation

The rules were tested against telemetry generated in the Wazuh/Sysmon environment.

Validation included:

- Windows failed-logon telemetry
- Controlled process-creation telemetry for the credential-access keyword detection
- Sysmon LSASS process-access telemetry
- Controlled Sysmon FileCreate telemetry
- Windows special-privilege logon telemetry
- Sigma correlation conversion and logic validation

The repeated-failed-logon correlation rule was successfully converted and its threshold logic was validated, although the configured threshold was not positively observed during testing.

## Sigma Conversion

The rules were validated using Sigma CLI and converted to OpenSearch-compatible query formats.

The project environment used Sigma CLI 3.1.0 and OpenSearch query targets.

## Important Scope

The validation environment used controlled and safe test activity. No actual credential-dumping operation was performed.

The detection rules are intended to demonstrate security monitoring and detection engineering concepts rather than claim successful compromise or production SOC deployment.
