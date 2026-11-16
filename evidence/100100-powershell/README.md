# Rule 100100 — PowerShell Execution Detection

**Detection:** Detects PowerShell process execution using Sysmon Event ID 1.

**Wazuh Rule ID:** `100100`

**Severity:** Level 7

**MITRE ATT&CK:** T1059.001 — PowerShell

## Evidence

- `alert.png` — Wazuh alert showing the detection.
- `event-detail.png` — Sysmon event details showing the PowerShell process.

## Result

Detection successfully tested in the Wazuh SOC home lab.
