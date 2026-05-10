
# OpenCanary Honeypot — Wazuh Integration

This section documents the deployment of an OpenCanary honeypot and its integration with Wazuh for centralized alerting and automated active response.

---

## Overview

OpenCanary is deployed as a decoy node on the home lab network. Any interaction with the honeypot — port scans, login attempts, service probes — generates alerts forwarded to Wazuh via custom decoders and rules. Confirmed hostile IPs are automatically blocked via Wazuh active response.

---

## OpenCanary Configuration

OpenCanary configured to emulate the following services:

| Service | Port | Purpose |
|---|---|---|
| SSH | 22 | Capture brute force attempts |
| HTTP | 80 | Capture web probing |
| FTP | 21 | Capture credential stuffing |
| Telnet | 23 | Capture legacy protocol abuse |

Config file: `opencanary.conf`

---

## Wazuh Integration

OpenCanary logs written to a local file, monitored by the Wazuh agent as a custom log source.

**ossec.conf entry on the agent:**
```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/opencanary.log</location>
</localfile>
```

---

## Custom Decoders

Custom decoders parse OpenCanary's JSON log format and extract relevant fields for Wazuh rule matching.

See: `decoders/opencanary_decoders.xml`

```xml
<decoder name="opencanary">
  <prematch>{"dst_host":</prematch>
  <regex>"src_host": "(\S+)"</regex>
  <order>srcip</order>
</decoder>
```

---

## Custom Alerting Rules

Custom rules trigger Wazuh alerts at severity level 10 on any honeypot interaction.

See: `rules/opencanary_rules.xml`

```xml
<rule id="100001" level="10">
  <decoded_as>opencanary</decoded_as>
  <description>OpenCanary honeypot interaction detected from $(srcip)</description>
  <group>honeypot,</group>
</rule>
```

---

## Active Response — Automatic IP Blocking

Wazuh active response configured to automatically block source IPs that trigger honeypot alerts using `firewall-drop`.

**ossec.conf (manager):**
```xml
<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <rules_id>100001</rules_id>
  <timeout>600</timeout>
</active-response>
```

- Blocking duration: 600 seconds (configurable)
- Blocking mechanism: iptables DROP rule applied automatically
- Logs confirm block applied via `/var/ossec/logs/active-responses.log`

---

## References

- [OpenCanary GitHub](https://github.com/thinkst/opencanary)
- [Wazuh Active Response Documentation](https://documentation.wazuh.com/current/user-manual/capabilities/active-response/index.html)
