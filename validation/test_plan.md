# Detection Rule Validation Test Plan

## Purpose

This document defines the validation approach for the Windows security monitoring detections implemented with Sigma, Sysmon, and Wazuh.

Validation focuses on generating or observing relevant Windows security telemetry and confirming that the expected event types and detection paths are available for investigation.

Testing uses controlled activity and benign telemetry rather than executing real credential-theft payloads.

---

## Rule 1: Windows Failed Logon

### Detection Objective

Detect failed Windows logon attempts using Windows Security Event ID 4625.

### Test Procedure

Generate controlled failed authentication attempts against the local Windows system.

Example:

```text
net use \\localhost /u:admin wrongpassword1
```

Repeat the controlled attempt multiple times.

### Expected Telemetry

Windows Event Viewer:

```text
Windows Logs → Security → Event ID 4625
```

### Validation Criteria

- Event ID 4625 is generated.
- The event is collected by the Wazuh agent.
- The event is available in the Wazuh/OpenSearch pipeline.
- The `windows_failed_logon` Sigma selection matches the event.

### False Positives

- Users entering incorrect passwords.
- Automated systems using incorrect credentials.

---

## Rule 2: Repeated Failed Logons from Single Source

### Detection Objective

Identify repeated failed logons from the same source IP using Sigma correlation logic.

### Correlation Logic

- Base rule: `windows_failed_logon`
- Grouping field: `IpAddress`
- Time window: 5 minutes
- Threshold: more than 5 events

### Validation Approach

Generate or identify multiple Event ID 4625 events originating from the same source.

### Expected Result

The correlation logic should identify a source producing more than five matching failed-logon events within the configured five-minute window.

### Validation Criteria

- Event ID 4625 telemetry is available.
- Events contain a source IP field suitable for grouping.
- The Sigma correlation rule converts successfully.
- The configured threshold and time window are represented correctly.

### Validation Note

The correlation threshold was not positively triggered during the documented test run. Therefore, the correlation logic is treated as implemented and conversion-validated rather than claiming a confirmed threshold alert.

---

## Rule 3: Suspicious Credential Dumping Tool Execution

### Detection Objective

Detect process-creation telemetry containing command-line indicators associated with credential-dumping tools.

### Safe Validation Approach

Validation uses benign command-line text containing detection keywords rather than executing an actual credential-dumping tool.

Example:

```text
powershell.exe -Command "Write-Host mimikatz"
```

or:

```text
cmd.exe /c echo sekurlsa test
```

### Expected Telemetry

Windows Security Event ID 4688:

```text
A new process has been created
```

### Validation Criteria

- Event ID 4688 telemetry is available.
- Command-line information is collected.
- The benign keyword test produces matching process-creation telemetry.
- The Sigma detection logic identifies the configured keyword.

### Normal Activity

Examples such as the following should not match the configured credential-dumping indicators:

```text
powershell.exe -Command "Get-Process"
```

and:

```text
notepad.exe
```

### Safety Note

No actual credential-dumping tool is required for this validation.

---

## Rule 4: Unusual LSASS Memory Access

### Detection Objective

Detect Sysmon ProcessAccess events involving `lsass.exe`.

### Telemetry Source

```text
Microsoft-Windows-Sysmon/Operational
Event ID 10 - ProcessAccess
```

### Validation Approach

Monitor Sysmon Event ID 10 telemetry for processes accessing `lsass.exe`.

The detection is validated through observed LSASS-access telemetry and Wazuh alerting.

### Expected Result

A matching Sysmon Event ID 10 should contain:

- Source process information.
- Target image information.
- `lsass.exe` as the target process.

### Validation Criteria

- Sysmon Event ID 10 telemetry is collected.
- `TargetImage` identifies `lsass.exe`.
- The event reaches Wazuh.
- The corresponding Wazuh detection/alert is generated.

### False Positives

- Legitimate security software.
- Endpoint Detection and Response tools.
- Authorized administrative or forensic activity.

---

## Rule 5: Browser and Vault Credential Store Access

### Detection Objective

Detect Sysmon FileCreate events involving Windows Credential Manager locations and browser password databases.

### Telemetry Source

```text
Microsoft-Windows-Sysmon/Operational
Event ID 11 - FileCreate
```

### Validation Approach

The detection pipeline is validated using controlled file-creation activity.

The validation confirms that Sysmon FileCreate telemetry reaches Wazuh and that the custom Wazuh rule can process Event ID 11.

### Expected Result

A matching Sysmon Event ID 11 should contain:

- The created file path.
- The creating process.
- File creation telemetry.

### Validation Criteria

- Sysmon Event ID 11 telemetry is collected.
- FileCreate events reach Wazuh.
- The custom Wazuh Event ID 11 rule processes the event.
- The alert is available in OpenSearch.

### Detection Scope

The Sigma rule contains indicators for:

```text
AppData\Local\Microsoft\Credentials
AppData\Roaming\Microsoft\Credentials
AppData\Local\Google\Chrome\User Data\Default\Login Data
```

### Validation Note

The documented validation confirms the Sysmon Event 11 → Wazuh alerting pipeline. It does not claim that an actual browser credential database was accessed.

---

## Rule 6: Special Privileges Assigned to Non-System User

### Detection Objective

Detect Windows Event ID 4672 events associated with non-system user accounts.

### Telemetry Source

```text
Windows Security
Event ID 4672 - Special privileges assigned to new logon
```

### Validation Approach

Review collected Event ID 4672 telemetry and verify that events associated with non-system accounts are available for detection.

### Expected Telemetry

The event may contain privileges such as:

- SeDebugPrivilege
- SeBackupPrivilege
- SeRestorePrivilege
- SeImpersonatePrivilege
- SeTakeOwnershipPrivilege

### Validation Criteria

- Event ID 4672 telemetry is collected.
- Non-system user activity is represented.
- The Sigma selection identifies Event ID 4672.
- System and service-account exclusions are applied.

### Investigation Context

Event ID 4672 indicates that special privileges were assigned to a new logon. It should be investigated together with related logon and account-management events when determining whether privilege escalation occurred.

---

## Overall Validation Workflow

```text
Controlled / Observed Activity
        ↓
Windows Security Events / Sysmon
        ↓
Wazuh Agent
        ↓
Wazuh Manager
        ↓
Wazuh Detection Rules
        ↓
OpenSearch
        ↓
Investigation / Validation
```

The validation process focuses on confirming telemetry collection, detection-rule matching, alert generation, and investigation visibility.