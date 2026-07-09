# Investigation Notes

## Investigation Objective

Investigate a simulated multi-stage PowerShell attack by correlating Sysmon Event IDs 1, 3, and 11 within Wazuh SIEM.

The purpose of this investigation was to reconstruct the sequence of events and determine whether the observed telemetry represented a single execution chain.

---

# Alert Summary

| Field | Value |
|-------|-------|
| Investigation Type | Multi-Event Correlation |
| Platform | Windows 11 |
| SIEM | Wazuh |
| Log Source | Sysmon |
| Investigation Status | Completed |

---

# Events Investigated

| Event ID | Description |
|----------|-------------|
| 1 | Process Creation |
| 3 | Network Connection |
| 11 | File Creation |

---

# Investigation Timeline

### Stage 1

PowerShell process launched.

↓

### Stage 2

PowerShell established an outbound HTTPS connection.

↓

### Stage 3

PowerShell created a local file.

↓

### Stage 4

Sysmon logged each event.

↓

### Stage 5

Wazuh Agent forwarded telemetry.

↓

### Stage 6

Events were indexed in Wazuh.

↓

### Stage 7

SOC investigation performed.

---

# Investigation Findings

## Process Creation

Verified the PowerShell process execution.

Reviewed:

- Process Name
- Parent Process
- Command Line
- Process GUID
- User

---

## Network Connection

Confirmed outbound network communication.

Reviewed:

- Destination IP
- Destination Port
- Protocol
- Process Name

---

## File Creation

Verified the creation of the local file.

Reviewed:

- File Path
- Process Name
- Timestamp

---

# Event Correlation

The Process GUID and timestamps confirmed that the Process Creation, Network Connection, and File Creation events were generated during the same PowerShell activity.

---

# MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---------|-----------|----|
| Execution | PowerShell | T1059.001 |
| Command and Control | Ingress Tool Transfer | T1105 |

---

# Indicators Collected

- Process Name
- Parent Process
- Command Line
- Destination IP
- Destination Port
- File Path
- Timestamp
- Process GUID
- User

---

# Investigation Outcome

The investigation successfully reconstructed the PowerShell execution timeline.

The correlated telemetry demonstrated how multiple Sysmon events can be analyzed together to understand endpoint activity.

No indicators of malicious behavior were identified, as the activity was intentionally generated for lab purposes.

---

# Lessons Learned

- Individual events provide limited context.
- Event correlation significantly improves investigation quality.
- Process GUIDs and timestamps are essential for timeline reconstruction.
- Wazuh simplifies endpoint investigations by centralizing Sysmon telemetry.
- Multi-event investigations closely reflect real-world SOC workflows.
