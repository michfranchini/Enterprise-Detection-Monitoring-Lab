## SOAR: Automated Response with Wazuh Active Response + pfSense

This document covers the SOAR implementation. When Wazuh detects a brute force attack via Rule 60122, it automatically executes a script to block the attacker's IP on the pfSense firewall. Zero manual intervention.

### 1. Active Response Configuration: `/var/ossec/etc/ossec.conf`

The Wazuh Manager is configured to trigger a custom script when Rule `60122` fires. This rule corresponds to "Logon failure - Unknown user or bad password", i.e., Windows Event ID 4625.

![Figure 1: Active Response Configuration](./screenshots/OSSEC_ACTIVE_RESPONSE_CONFIG.png)
*Figure 1: `ossec.conf` defines the `pfblock` command and links it to Rule 60122.*

**Configuration breakdown**:
```xml
<command>
  <name>pfblock</name>
  <executable>pfblock.sh</executable>
  <timeout_allowed>yes</timeout_allowed>
</command>

<active-response>
  <command>pfblock</command>
  <location>server</location>
  <rules_id>60122</rules_id>
  <timeout>600</timeout>
</active-response>

<rules_id>60122</rules_id>: Triggers on brute force detection.<location>server</location>: The script runs on the Wazuh Manager itself.<timeout>600</timeout>: The IP is blocked for 10 minutes, then automatically unblocked.<executable>pfblock.sh</executable>: The script responsible for firewall interaction.
