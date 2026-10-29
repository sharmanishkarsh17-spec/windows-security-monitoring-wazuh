\# Detection Validation Matrix



\## Windows Security Monitoring \& Threat Detection Using Sigma and Wazuh



| Rule | Detection | Telemetry | Validation Status | Evidence |

|---|---|---|---|---|

| Rule 1 | Windows Failed Logon | Security Event ID 4625 | \*\*Validated\*\* | Failed-logon telemetry observed in Wazuh/OpenSearch |

| Rule 2 | Repeated Failed Logons from Single Source | Security Event ID 4625 + Sigma correlation | \*\*Logic Validated\*\* | Sigma correlation converted successfully; threshold trigger not positively observed |

| Rule 3 | Suspicious Credential Dumping Tool Execution | Security Event ID 4688 | \*\*Safely Validated\*\* | Controlled keyword-based process telemetry used for detection validation |

| Rule 4 | Unusual LSASS Memory Access | Sysmon Event ID 10 | \*\*Validated\*\* | LSASS-access telemetry observed and Wazuh alert generated |

| Rule 5 | Browser and Vault Credential Store Access | Sysmon Event ID 11 | \*\*Pipeline Validated\*\* | Controlled FileCreate telemetry reached Wazuh custom rule |

| Rule 6 | Special Privileges Assigned to Non-System User | Security Event ID 4672 | \*\*Validated\*\* | Privileged-logon telemetry observed in Wazuh/OpenSearch |



\---



\## Rule 1 — Windows Failed Logon



\*\*Event ID:\*\* 4625  

\*\*Status:\*\* Validated



\### Validation



Windows failed-logon telemetry was observed in the Wazuh/OpenSearch pipeline.



The detection identifies Event ID 4625 while excluding common system and service accounts.



\### Result



\*\*PASS — Telemetry and detection logic validated.\*\*



\---



\## Rule 2 — Repeated Failed Logons from Single Source



\*\*Base Event:\*\* 4625  

\*\*Correlation:\*\* More than 5 events from the same `IpAddress` within 5 minutes  

\*\*Status:\*\* Logic Validated



\### Validation



The underlying Event ID 4625 telemetry was confirmed and the Sigma correlation rule was successfully converted.



The configured correlation threshold was not positively triggered during the documented validation run.



\### Result



\*\*PASS — Correlation logic validated.\*\*



\*\*Note:\*\* This result does not claim that the threshold condition generated a confirmed alert during testing.



\---



\## Rule 3 — Suspicious Credential Dumping Tool Execution



\*\*Event ID:\*\* 4688  

\*\*Status:\*\* Safely Validated



\### Validation



The detection was tested using benign command-line text containing credential-access keywords.



Example validation concept:



```text

powershell.exe -Command "Write-Host mimikatz"

```



This validates the ability of the detection logic to identify configured command-line indicators without executing an actual credential-dumping tool.



\### Result



\*\*PASS — Detection concept validated using controlled process telemetry.\*\*



\---



\## Rule 4 — Unusual LSASS Memory Access



\*\*Event ID:\*\* Sysmon 10  

\*\*Status:\*\* Validated



\### Validation



Sysmon Event ID 10 telemetry involving `lsass.exe` was observed.



The event was collected by Wazuh and generated a corresponding alert.



Legitimate security software activity was observed during validation, demonstrating the importance of reviewing the source process when investigating this detection.



\### Result



\*\*PASS — Sysmon → Wazuh LSASS-access monitoring validated.\*\*



\---



\## Rule 5 — Browser and Vault Credential Store Access



\*\*Event ID:\*\* Sysmon 11  

\*\*Status:\*\* Pipeline Validated



\### Validation



Controlled FileCreate activity was generated and collected through Sysmon Event ID 11.



The event reached Wazuh and was processed by the custom Wazuh Event ID 11 rule.



\### Result



\*\*PASS — Sysmon FileCreate → Wazuh alerting pipeline validated.\*\*



\### Scope Note



The validation confirms the telemetry and alerting pipeline. It does not claim that an actual browser password database was accessed.



\---



\## Rule 6 — Special Privileges Assigned to Non-System User



\*\*Event ID:\*\* 4672  

\*\*Status:\*\* Validated



\### Validation



Windows Security Event ID 4672 telemetry was observed in Wazuh/OpenSearch.



The collected events included special privileges associated with user logons, including privileges such as:



\- `SeDebugPrivilege`

\- `SeBackupPrivilege`

\- `SeRestorePrivilege`

\- `SeImpersonatePrivilege`

\- `SeTakeOwnershipPrivilege`



\### Result



\*\*PASS — Privileged-logon telemetry and detection logic validated.\*\*



\### Investigation Note



Event ID 4672 represents special privileges assigned to a new logon. It should be correlated with related authentication and account-management events when investigating possible privilege escalation.



\---



\## Overall Validation Summary



| Category | Result |

|---|---|

| Windows Security telemetry | Validated |

| Sysmon telemetry | Validated |

| Wazuh agent collection | Validated |

| Wazuh alerting | Validated |

| OpenSearch investigation | Validated |

| Sigma rule conversion | Validated |

| Sigma correlation logic | Validated |

| Safe detection testing | Validated |



\### Overall Assessment



The project demonstrates an end-to-end Windows security monitoring pipeline in which Windows Security Events and Sysmon telemetry are collected through Wazuh, processed for detection, and made available through OpenSearch for investigation.



The validation emphasizes detection engineering and telemetry coverage rather than execution of real credential-theft techniques.

