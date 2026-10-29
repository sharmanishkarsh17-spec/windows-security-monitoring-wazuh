# Sample Investigation Queries

## Purpose

This document contains example queries used to investigate Windows security and Sysmon telemetry in OpenSearch.

## Windows Failed Logons — Event ID 4625

data.win.system.eventID:4625

Use this query to identify failed Windows authentication events.

## Process Creation — Event ID 4688

data.win.system.eventID:4688

Use this query to investigate Windows process-creation telemetry.

## Sysmon LSASS Access — Event ID 10

data.win.system.eventID:10

Use this query to investigate Sysmon process-access events, including activity targeting lsass.exe.

## Sysmon FileCreate — Event ID 11

data.win.system.eventID:11

Use this query to investigate Sysmon file-creation telemetry.

## Custom Wazuh Rule — 100011

rule.id:100011

Use this query to identify events processed by the custom Sysmon Event ID 11 Wazuh rule.

## Special Privileges — Event ID 4672

data.win.system.eventID:4672

Use this query to investigate Windows special-privilege logon telemetry.

## Investigation Fields

Useful fields to inspect during investigation include:

- Event ID
- Target user
- Source IP
- Process name
- Command line
- Target image
- Target filename
- Wazuh rule ID
- Privilege information

## Investigation Approach

1. Identify the relevant event type.
2. Filter the event source or Event ID.
3. Review the user and process context.
4. Inspect source IP, command line, target image, or target filename where available.
5. Check the associated Wazuh rule.
6. Compare the event with the corresponding Sigma detection logic.
7. Determine whether the activity requires further investigation.
