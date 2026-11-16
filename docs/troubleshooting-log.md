# Troubleshooting Log

## Issue 1: Sysmon events not visible in Wazuh
**Symptom:** Sysmon generated events locally, the agent was Active, but nothing
from Sysmon appeared in the dashboard.

**What I checked**
- Agent connection: connected to the manager on 1514/tcp, status Active.
- Sysmon: Event ID 1 (process creation) and Event ID 5 (termination) present
  in Event Viewer.
- Agent config: added the `Microsoft-Windows-Sysmon/Operational` event channel
  to `ossec.conf` (found and fixed a typo, `log_form` instead of `log_format`).
- Manager: enabled `logall_json` so every event is archived, not only alerts.
- Confirmed `archives.json` exists and is being written.

**What I learned**
- The `wazuh-alerts-*` index holds only events that matched a rule, so an empty
  result there doesn't mean the data isn't arriving.
- Archives need their own index pattern, which has to be created manually.
- An Active agent doesn't prove a specific log channel is being forwarded.

**Resolution:** <fill in once fixed>
