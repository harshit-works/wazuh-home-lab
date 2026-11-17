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

## Issue 2: Locked out of the Wazuh dashboard
Symptom: the dashboard rejected the admin login.
Cause: the original admin password was not available, and the first reset attempt
left the indexer unable to read a folder created by root.
Fix: restored the folder permissions, loaded the new admin credentials into the
indexer's security configuration, restarted the dashboard, and checked that Filebeat
could still send alerts to the indexer.
Lessons: Wazuh config folders need sudo; copy long commands instead of typing them;
quote passwords that contain special characters; after a password change, verify
every component that uses it, not just the login page.

## Issue 3: Wazuh rules file would not load (XML not closed)
Symptom: wazuh-analysisd -t failed with ERROR: (1226): XMLERR: End of file and some elements were not closed (line 48), followed by CRITICAL: (1220): Error loading the rules: 'etc/rules/local_rules.xml'.
Cause: The tags in local_rules.xml were not balanced. A new <group name="local,sysmon,"> block was pasted below existing rules, which left a <group> open at the end of the file.
Fix: Edited the bottom of the file so every opened tag had a matching closing tag, then re-ran the config test until it printed nothing.
Lessons: XML works like brackets in maths: every opening tag needs its own closing tag. Always run sudo /var/ossec/bin/wazuh-analysisd -t before restarting the manager, and do not restart while the test is failing.

## Issue 4: 'group' is not a valid element
Symptom: After adding an extra </group> to fix Issue 3, the test failed with ERROR: Invalid configuration. 'group' is not a valid element.
Cause: The extra closing tag exposed the real problem. A second <group name="local,sysmon,"> wrapper sat inside the existing outer group, and Wazuh does not allow nested groups.
Fix: Deleted the second <group name="local,sysmon,"> line and one of the two closing </group> lines, so only one wrapper remained. The new rule now sits directly inside the existing group.
Lessons: Adding tags to silence an error can hide the real cause. Before pasting a new rule into local_rules.xml, check whether the file already has a wrapper group, and do not add a second one.

## Issue 5: Custom rule 100102 never appeared in the dashboard
Symptom: grep -c '"id":"100102"' alerts.json returned 0 and searching rule.id: 100102 showed nothing. Only the built-in rule 92052 appeared. The config test also warned: Rule ID '61603' is duplicated. Only the first occurrence will be considered.
Cause: While testing a different parent rule, 61603 was typed into the rule's own ID (<rule id="61603">) instead of into <if_sid>. That ID already belongs to a built-in Wazuh rule, so Wazuh ignored the custom rule. A later sed command also failed to fix it because of a typo (level-"10" instead of level="10").
Fix: Restored the ID with a line-specific command, sudo sed -i '37s/61603/100102/' /var/ossec/etc/rules/local_rules.xml, confirmed with grep -n "rule id=" local_rules.xml, then ran the test, restarted the manager, and generated a fresh event on Windows. Rule 100102 then appeared in the dashboard.
