# Rule 100101 — Suspicious PowerShell Arguments

**Detection:** Detects suspicious PowerShell command-line arguments, including hidden-window execution and encoded commands.

**Wazuh Rule ID:** `100101`

**Severity:** Level 10

**MITRE ATT&CK:** T1059.001 — PowerShell

## Evidence

- `alert.png` — Wazuh alert showing Rule 100101.
- `event-detail.png` — Raw Sysmon event showing the suspicious command line.

## Result

Detection successfully tested in the Wazuh SOC home lab.
