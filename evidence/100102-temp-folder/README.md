# Rule 100102 — Execution from Temporary Folders

## Objective
Detect processes executed from suspicious temporary directories on Windows using Sysmon Event ID 1.

## Detection Details
- **Wazuh Rule ID:** `100102`
- **Rule Level:** 10
- **MITRE ATT&CK:** T1204 — User Execution
- **Log Source:** Sysmon Process Creation (Event ID 1)
- **Detection Field:** `win.eventdata.image`

## Detection Logic
The custom Wazuh rule identifies process execution from:
- `C:\Temp\`
- `AppData\Local\Temp\`

It excludes `C:\Windows\Temp\`.

## Test Procedure
1. Created `C:\Temp` on the Windows VM.
2. Copied Notepad into the folder as `svhost.exe`.
3. Executed the copied program.
4. Checked Wazuh alerts for Rule ID `100102`.

## Expected Detection
**Process executed from a temporary folder**

## Evidence
- `alert.png` — Wazuh alert showing Rule ID 100102.
- `event-detail.png` — Sysmon event details showing the executable path.

## Result
The temporary-folder execution detection was successfully tested in the Wazuh SOC home lab.
