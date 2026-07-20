# OpenCanary Honeypot

This section documents the deployment of an OpenCanary honeypot on a cloud-hosted DigitalOcean droplet and its integration with the Wazuh SIEM for centralized alerting, threat intelligence enrichment, and automated IP blocking via Fail2ban.

---

## Overview

OpenCanary is deployed as an internet-facing decoy server on a dedicated DigitalOcean droplet (Ubuntu 24.04 LTS, IP: 159.89.53.117). Any interaction with the honeypot — port scans, login attempts, service probes, credential stuffing — generates alerts that are forwarded to the Wazuh SIEM via the Wazuh agent running on the same droplet. Confirmed hostile IPs are automatically blocked via Fail2ban and reported to Discord with full AbuseIPDB threat intelligence enrichment.

The honeypot is intentionally wide open to the internet. Every alert it generates is a real threat — there are no false positives from legitimate traffic.

---

## Infrastructure

| Component | Details |
|---|---|
| Host | DigitalOcean Ubuntu 24.04 LTS |
| Public IP | 159.89.53.117 |
| Wazuh Agent | Agent 001 (ubuntu-server-po) reporting to 142.93.251.205 |
| Firewall | Wide open — all honeypot ports exposed to the internet |
| Fail2ban | Installed and active, Discord notifications on ban/unban |

---

## OpenCanary Services

OpenCanary emulates the following services to attract a wide variety of attack types:

| Service | Port | What Attackers See |
|---|---|---|
| SSH (fake) | 2222 | Ubuntu SSH server version 5.1 |
| FTP | 21 | FTP server ready to accept file transfers |
| HTTP | 80 | Apache web server version 2.2.22 on Ubuntu |
| HTTPS | 443 | HTTPS server with self-signed certificate |
| MySQL | 3306 | MySQL database version 5.5.43 |
| Telnet | 23 | Old school remote access terminal |
| RDP | 3389 | Windows remote desktop server |
| Redis | 6379 | Redis cache server (targeted by crypto miners) |
| VNC | 5900 | Virtual desktop access |
| HTTP Proxy | 8080 | Squid proxy server |
| SMB | 445 | Windows file sharing server |
| Portscan Detection | N/A | Detects anyone running Nmap against the server |

> **NOTE:** The real SSH service runs on port 22 for management access to the droplet. OpenCanary's fake SSH runs on port 2222 to avoid conflict.

Config file: `opencanary.conf`

---

## Wazuh Integration

The Wazuh agent installed on the honeypot droplet monitors OpenCanary's log output and forwards events to the cloud Wazuh manager at 142.93.251.205 over port 1514.

**ossec.conf entry on the honeypot agent:**

```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/opencanary.log</location>
</localfile>
```

### Custom Decoders

Custom decoders parse OpenCanary's JSON log format and extract relevant fields for Wazuh rule matching.

See: `decoders/opencanary_decoders.xml`

```xml
<decoder name="opencanary">
  <prematch>{"dst_host":</prematch>
  <regex>"src_host": "(\S+)"</regex>
  <order>srcip</order>
</decoder>
```

### Custom Alerting Rules

Custom rules trigger Wazuh alerts on any honeypot interaction. These map to rule IDs 87103 through 87106 which route to the Discord #cyber-news channel.

See: `rules/opencanary_rules.xml`

```xml
<rule id="100001" level="10">
  <decoded_as>opencanary</decoded_as>
  <description>OpenCanary honeypot interaction detected from $(srcip)</description>
  <group>honeypot,</group>
</rule>
```

---

## Discord Alerting

Honeypot hits route to two Discord channels:

| Channel | Trigger | Content |
|---|---|---|
| #cyber-news | OpenCanary rules 87103-87106 | Raw Wazuh alert with rule description, agent, source IP |
| #abuseipdb-alerts | SSH brute force rules 5710, 5712, 5762, 5763 | Full AbuseIPDB threat intelligence report including abuse score, country, ISP, total reports, and direct link |

The AbuseIPDB integration fires after the same rule triggers 3 or more times to conserve the free tier daily quota of 1,000 lookups. Alerts are suppressed between 11 PM and 9 AM EST.

---

## Fail2ban — Automatic IP Blocking

Fail2ban is installed on the honeypot and configured to automatically ban IPs that repeatedly fail SSH authentication. A custom Discord action fires a notification to the #blocked-ips channel on every ban and unban event.

**jail.local configuration:**

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

**Discord notification format:**

```
🍯 Fail2ban BANNED IP | Honeypot
IP: x.x.x.x
Jail: sshd
Time: [timestamp]
```

The honeypot uses a 🍯 emoji in notifications. The Wazuh server uses a 🛡️ emoji. This makes it easy to tell at a glance which server issued the ban.

---

## Attack Volume

The honeypot receives thousands of automated attack attempts daily. Common patterns observed:

- SSH brute force with credential wordlists (most common)
- Username patterns indicate botnet targeting: root, admin, ubuntu, linux, deploy, pi, oracle, postgres, git, jenkins
- The username pi specifically indicates bots targeting Raspberry Pi devices with default credentials
- Traffic originates from data centers in China, Russia, Germany, Romania, Netherlands, and India

All attack data is included in the daily AI threat intelligence report posted to Discord at 9 AM EST.
