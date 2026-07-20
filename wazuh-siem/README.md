# Wazuh SIEM — Setup and Configuration

This section documents the deployment and configuration of a self-hosted Wazuh 4.14.5 SIEM environment on cloud infrastructure, including manager setup, multi-agent enrollment, centralized FIM via agent groups, Discord alerting, AbuseIPDB integration, SSL configuration, cloud firewall hardening, and Fail2ban protection.

---

## Architecture

| Role | Host | OS | IP |
|---|---|---|---|
| Wazuh Manager | WazuhCMDC (DigitalOcean) | Ubuntu 22.04 LTS | 142.93.251.205 |
| Agent 001 | ubuntu-server-po (DigitalOcean) | Ubuntu 24.04 LTS | 159.89.53.117 |
| Agent 002 | PATPC | Windows 11 Home | 192.168.40.134 |
| Agent 003 | eljefe | Kali GNU/Linux | 192.168.40.x |
| Agent 004 | PatHP55 | Windows 11 Home | 192.168.40.x |

The Wazuh manager was migrated from a local VirtualBox VM to a DigitalOcean cloud droplet (4GB RAM, 2 vCPU, 80GB SSD) running Wazuh 4.14.5. This allows the SIEM to run 24/7 independently of home machines being powered on. The domain wazuhseclab.xyz points to the manager with a valid Let's Encrypt SSL certificate.

---

## Manager Setup

- Deployed Wazuh 4.14.5 on a DigitalOcean Ubuntu 22.04 LTS droplet using the official Wazuh installation script
- Components installed: Wazuh Manager, Wazuh Indexer, Wazuh Dashboard, Filebeat
- Configured Filebeat to ship alerts from the manager to the OpenSearch indexer with correct field mappings
- Obtained Let's Encrypt SSL certificate via Certbot for wazuhseclab.xyz
- Configured the Wazuh dashboard to use the Let's Encrypt certificate instead of the default self-signed cert
- Created a DigitalOcean cloud firewall (wazuh-server-fw) to restrict access to trusted sources only

### Cloud Firewall Rules

| Port | Protocol | Allowed Source | Purpose |
|---|---|---|---|
| 22 | TCP | Home IP only | SSH management |
| 443 | TCP | Home IP only | Wazuh dashboard |
| 1514 | TCP | Honeypot + home IP | Agent communication |
| 1515 | TCP | Honeypot + home IP | Agent enrollment |
| 55000 | TCP | Home IP only | Wazuh API |

---

## Agent Enrollment

Agents enrolled across Windows and Linux endpoints using the Wazuh agent installer with the cloud manager IP specified at install time.

**Windows (PowerShell):**

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.x.msi -OutFile wazuh-agent.msi
msiexec /i wazuh-agent.msi WAZUH_MANAGER="142.93.251.205" /q
NET START WazuhSvc
```

**Linux:**

```bash
WAZUH_MANAGER="142.93.251.205" apt-get install wazuh-agent
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

---

## Agent Groups and Centralized FIM

Agent groups are used to push centralized FIM configurations from the manager to specific agents without touching each machine individually.

**Groups configured:**

| Group | Members | Config File |
|---|---|---|
| windows-agents | PATPC (002), PatHP55 (004) | agent-configs/windows-agents.conf |
| kali-agent | eljefe (003) | agent-configs/kali-agent.conf |

**Creating groups and assigning agents:**

```bash
sudo /var/ossec/bin/agent_groups -a -g windows-agents
sudo /var/ossec/bin/agent_groups -a -g kali-agent
sudo /var/ossec/bin/agent_groups -a -i 002 -g windows-agents
sudo /var/ossec/bin/agent_groups -a -i 004 -g windows-agents
sudo /var/ossec/bin/agent_groups -a -i 003 -g kali-agent
```

**Windows FIM paths monitored:**

| Path | Mode |
|---|---|
| Downloads, Desktop, Documents | Realtime |
| System32, Program Files, Program Files (x86) | Scheduled every 5 hours |
| Startup folder | Scheduled every 5 hours |
| HKLM Run and RunOnce registry keys | Scheduled every 5 hours |

**Kali FIM paths monitored:**

| Path | Mode |
|---|---|
| /root, /home/eljefe | Realtime |
| /etc, /usr/bin, /tmp | Scheduled every 5 hours |

> **NOTE:** The file limit was raised to 200,000 entries in both agent group configs after PATPC hit the default 100,000 limit.

---

## Discord Alerting

Alerts route to Discord via custom Python integration scripts. Migrated from Slack after hitting the free tier message limit.

**Integration scripts:**

| Script | Purpose |
|---|---|
| integrations/custom-discord | Formats and sends general Wazuh alerts to Discord via webhook |
| integrations/custom-virustotal-ip | Queries AbuseIPDB on SSH brute force triggers and posts enriched IP reports |

**ossec.conf integration blocks:**

```xml
<!-- Commandline Alerts Channel (Level 5+) -->
<integration>
  <name>custom-discord</name>
  <hook_url>WEBHOOK_URL</hook_url>
  <alert_format>json</alert_format>
  <level>5</level>
</integration>

<!-- High Alerts Channel (Level 10+) -->
<integration>
  <name>custom-discord</name>
  <hook_url>WEBHOOK_URL</hook_url>
  <alert_format>json</alert_format>
  <level>10</level>
</integration>

<!-- File Monitoring Channel -->
<integration>
  <name>custom-discord</name>
  <hook_url>WEBHOOK_URL</hook_url>
  <alert_format>json</alert_format>
  <rule_id>550,551,552,553,554,555,556,557,558</rule_id>
</integration>

<!-- OpenCanary Honeypot Hits -->
<integration>
  <name>custom-discord</name>
  <hook_url>WEBHOOK_URL</hook_url>
  <alert_format>json</alert_format>
  <rule_id>87103,87104,87105,87106</rule_id>
</integration>

<!-- AbuseIPDB Threat Intelligence -->
<integration>
  <name>custom-virustotal-ip</name>
  <hook_url>WEBHOOK_URL</hook_url>
  <api_key>ABUSEIPDB_KEY</api_key>
  <rule_id>5710,5712,5762,5763</rule_id>
  <alert_format>json</alert_format>
</integration>
```

---

## Fail2ban

Fail2ban is installed on the Wazuh server as a second layer of SSH protection alongside the cloud firewall. A custom Discord action fires ban and unban notifications to the #blocked-ips channel.

```ini
[DEFAULT]
bantime = 1h
findtime = 10m
maxretry = 3

[sshd]
enabled = true
port = 22
logpath = /var/log/auth.log
maxretry = 3
action = %(action_)s
         discord-notify
```

The Wazuh server uses a shield emoji in ban notifications to distinguish it from the honeypot which uses a honeypot emoji.

---

## Daily AI Threat Intelligence Report

A cron job runs the daily threat report script at 9 AM EST every day:

```
0 14 * * * /var/ossec/framework/python/bin/python3 /usr/local/bin/daily-threat-report.py
```

The script pulls the last 24 hours of alerts from the Wazuh OpenSearch indexer at `https://127.0.0.1:9200`, queries AbuseIPDB for the top attacking IPs, generates an AI executive summary via Claude Haiku, and posts the full report to the Discord #daily-threat-report channel.

See: `scripts/daily-threat-report.py`

---

## Log Collection

Wazuh configured to collect:

- Windows Event Logs (Security, System, Application) via Wazuh agent on PATPC and PatHP55
- Linux syslog, auth.log, and journald via Wazuh agent on eljefe and ubuntu-server-po
- OpenCanary honeypot JSON log output via custom localfile entry on ubuntu-server-po
- Wazuh manager internal logs including active-responses.log

---

## Troubleshooting Notes

- **ossec.conf XML errors** — The config must have a single `<ossec_config>` block. Multiple blocks cause the manager to fail on start. Always validate with `sudo xmllint --noout /var/ossec/etc/ossec.conf` before restarting
- **Filebeat segfaults** — Filebeat 7.10.2 is unstable on Ubuntu 22.04. Configured with `Restart=always` and `RestartSec=10` so it auto-recovers from crashes
- **wazuh-modulesd failing** — If the API returns a 500 error about modulesd, run `sudo /var/ossec/bin/wazuh-control restart` to restart all daemons together rather than just the systemd service
- **Agent connectivity** — Home agents connect over port 1514 from the home IP. Ensure the DO cloud firewall allows the home IP on ports 1514 and 1515 or agents will silently fail to connect
- **API password reset** — Wazuh API passwords are stored as scrypt hashes in `/var/ossec/api/configuration/security/rbac.db`. Use werkzeug to generate a new hash and update via sqlite3 if the password is lost
- **integrations queue** — The directory `/var/ossec/queue/integrations/` must exist and be owned by `wazuh:wazuh` for the integratord to process alerts. Create it manually if missing

---

## Key Commands

```bash
# Check all daemon status
sudo /var/ossec/bin/wazuh-control status

# Restart all Wazuh daemons
sudo /var/ossec/bin/wazuh-control restart

# List connected agents
sudo /var/ossec/bin/agent_control -l

# Force FIM scan on an agent
sudo /var/ossec/bin/agent_control -r -u 002

# Validate ossec.conf before restarting
sudo xmllint --noout /var/ossec/etc/ossec.conf

# Check integrations log
sudo tail -f /var/ossec/logs/integrations.log

# Check OpenSearch alert index
curl -k -u admin:PASSWORD "https://127.0.0.1:9200/_cat/indices?v" | grep wazuh-alerts
```

---

## References

- [Wazuh Documentation](https://documentation.wazuh.com)
- [Wazuh Agent Enrollment Guide](https://documentation.wazuh.com/current/user-manual/agent-enrollment/index.html)
- [Wazuh Agent Groups](https://documentation.wazuh.com/current/user-manual/reference/centralized-configuration.html)
- [AbuseIPDB API](https://www.abuseipdb.com/api.html)
- [Anthropic Claude API](https://docs.anthropic.com)
