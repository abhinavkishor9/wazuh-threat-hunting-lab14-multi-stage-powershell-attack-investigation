# Troubleshooting Notes

## Issue 1 – PowerShell Command Failed

### Problem

The PowerShell command failed to execute or returned an error.

### Possible Causes

- Incorrect command syntax
- No Internet connectivity
- Temporary website unavailability
- PowerShell execution interrupted

### Resolution

Verified the command syntax and Internet connectivity before re-running the command.

Example:

```powershell
Invoke-WebRequest https://example.com
```

---

# Issue 2 – Working Directory Not Created

### Problem

The `C:\SOCLab` directory was not created.

### Possible Causes

- Typographical error
- Insufficient permissions
- Directory already existed

### Resolution

Recreated the directory using:

```powershell
New-Item -ItemType Directory -Path C:\SOCLab -Force
```

Verified:

```powershell
Get-ChildItem C:\
```

---

# Issue 3 – File Not Created

### Problem

The expected file (`example.txt`) was not present.

### Possible Causes

- PowerShell command failed
- Incorrect file path
- Permission issue

### Resolution

Verified file creation:

```powershell
Get-ChildItem C:\SOCLab
```

Re-created the file:

```powershell
"Downloaded content from example.com" | Out-File C:\SOCLab\example.txt
```

---

# Issue 4 – No Process Creation Event (Sysmon Event ID 1)

### Problem

The PowerShell execution was not visible in Sysmon or Wazuh.

### Possible Causes

- Sysmon Event ID 1 not enabled
- Incorrect search query
- Time range did not include the event

### Resolution

Verified Event ID 1:

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" `
-FilterXPath "*[System[(EventID=1)]]" `
-MaxEvents 10
```

Wazuh search:

```
data.win.system.eventID:1
```

---

# Issue 5 – No Network Connection Event (Sysmon Event ID 3)

### Problem

No Event ID 3 appeared in Wazuh Threat Hunting.

### Possible Causes

- Network request failed
- Sysmon Event ID 3 disabled
- Incorrect time range
- Wazuh ingestion delay

### Resolution

Verified Event ID 3:

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" `
-FilterXPath "*[System[(EventID=3)]]" `
-MaxEvents 10
```

Expanded the Discover time range to:

- Last 15 Minutes
- Last 1 Hour
- Today

Waited 30–60 seconds and refreshed Wazuh.

---

# Issue 6 – No File Creation Event (Sysmon Event ID 11)

### Problem

The file creation event was not found.

### Possible Causes

- File creation failed
- Event ID 11 disabled
- Incorrect search query

### Resolution

Verified Event ID 11:

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" `
-FilterXPath "*[System[(EventID=11)]]" `
-MaxEvents 10
```

Confirmed the file existed:

```powershell
Get-ChildItem C:\SOCLab
```

---

# Issue 7 – No Results in Wazuh

### Problem

Threat Hunting returned no matching documents.

### Possible Causes

- Wazuh Agent had not yet forwarded the events
- Incorrect Discover time range
- Search filters too restrictive

### Resolution

- Waited approximately one minute.
- Refreshed Discover.
- Expanded the time range.
- Verified that the Wazuh Agent service was running.

Useful searches:

```
data.win.system.eventID:1
```

```
data.win.system.eventID:3
```

```
data.win.system.eventID:11
```

```
powershell.exe
```

```
example.txt
```

---

# Issue 8 – Multiple PowerShell Events

### Problem

Several PowerShell events were generated, making it difficult to identify the correct activity.

### Resolution

Correlated events using:

- Timestamp
- Process GUID
- Process ID
- Command Line
- User
- Destination IP
- File Path

This allowed the complete execution chain to be reconstructed.

---

# Useful Commands

## Verify Sysmon Service

```powershell
Get-Service Sysmon64
```

---

## Verify Wazuh Agent

```powershell
Get-Service WazuhSvc
```

---

## View Process Creation Events

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" `
-FilterXPath "*[System[(EventID=1)]]"
```

---

## View Network Connection Events

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" `
-FilterXPath "*[System[(EventID=3)]]"
```

---

## View File Creation Events

```powershell
Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" `
-FilterXPath "*[System[(EventID=11)]]"
```

---

# Lessons Learned

- A single PowerShell command can generate multiple Sysmon events.
- Event correlation provides significantly more context than investigating individual alerts.
- Process GUIDs and timestamps are critical for reconstructing attack timelines.
- Wazuh centralizes endpoint telemetry, enabling efficient SOC investigations.
- Correct time ranges and event correlation are essential for accurate threat hunting.
- Multi-stage investigations better reflect real-world SOC workflows than isolated event analysis.
