# Cybersecurity Home Lab — projectPat003

A hands-on cybersecurity home lab environment built for threat detection, SIEM operations, honeypot deployment, and offensive security testing. This repository documents configurations, custom scripts, Discord integrations, and threat intelligence automation developed through active experimentation on live infrastructure.

---

## Lab Overview

| Component | Details |
|---|---|
| SIEM Platform | Wazuh 4.14.5 (cloud-hosted on DigitalOcean, Ubuntu 22.04 LTS) |
| SIEM Domain | wazuhseclab.xyz (Let's Encrypt SSL) |
| Monitored Endpoints | Windows (PATPC, PatHP55), Kali Linux (eljefe) |
| Honeypot | OpenCanary (DigitalOcean, Ubuntu 24.04 LTS) |
| Offensive Testing | Kali Linux (eljefe) |
| Alert Platform | Discord (migrated from Slack) |
| Threat Intelligence | AbuseIPDB API + Claude AI daily report |
| Network | 192.168.40.x home subnet, agents connect to cloud SIEM |

---

## Repository Structure

```
cybersecurity-home-lab/
├── README.md                          ← You are here
├── wazuh-siem/
│   ├── README.md                      ← Setup and architecture notes
│   ├── ossec.conf                     ← Manager configuration (single ossec_config block)
│   ├── agent-configs/
│   │   ├── windows-agents.conf        ← Centralized FIM config for PATPC and PatHP55
│   │   └── kali-agent.conf            ← Centralized FIM config for eljefe
│   └── integrations/
│       ├── custom-discord             ← General Wazuh alert Discord webhook script
│       └── custom-virustotal-ip       ← AbuseIPDB IP threat intelligence script
├── opencanary-honeypot/
│   ├── README.md                      ← Deployment and integration notes
├── kali-scripts/
│   ├── README.md
│   └── scripts/                       ← Offensive testing scripts
└── threat-intel-reports/
    ├── README.md
    └── samples/                       ← Redacted threat intel report samples
```

---

## Key Capabilities Demonstrated

**SIEM Deployment** — End-to-end Wazuh 4.14.5 installation on a cloud DigitalOcean droplet including manager, indexer, dashboard, and Filebeat. Migrated from a local VirtualBox VM to always-on cloud infrastructure. Custom SSL certificate via Let's Encrypt for the dashboard.

**Agent Management** — Four Wazuh agents across Windows 11 and Kali Linux reporting to the cloud SIEM over port 1514. Agent groups used to push centralized FIM configurations to Windows and Kali agents without touching each machine individually.

**File Integrity Monitoring** — Realtime monitoring of user directories (Downloads, Desktop, Documents, /root, /home/eljefe) and scheduled scans of system directories (System32, Program Files, /etc, /usr/bin). Windows registry run keys monitored for persistence detection.

**Honeypot Integration** — OpenCanary deployed on a separate DigitalOcean droplet as a fully internet-facing decoy node with 12 fake services (SSH, FTP, HTTP, HTTPS, MySQL, RDP, SMB, Telnet, Redis, VNC, HTTP Proxy, portscan detection). Alerts forwarded to Wazuh via custom decoders and rules.

**Discord Alerting** — Migrated from Slack (hit free tier message limits) to Discord with no message limits. Custom Python scripts format and route alerts to 8 dedicated channels based on alert type and severity.

**AbuseIPDB Threat Intelligence** — Custom integration script queries AbuseIPDB on SSH brute force triggers and posts enriched IP reports to Discord including abuse score, country, ISP, and total prior reports. Rate-limited to 3 triggers minimum with time-based suppression (11 PM to 9 AM EST) to conserve the free tier quota.

**Daily AI Threat Report** — Python script runs via cron at 9 AM EST every day. Pulls last 24 hours of alerts from the Wazuh OpenSearch indexer, queries AbuseIPDB for top attacking IPs, generates an AI executive summary via Claude Haiku, and posts a full structured report to Discord.

**Fail2ban Integration** — Installed on both the Wazuh server and honeypot. Custom Discord action script fires ban and unban notifications to a dedicated channel with server-specific emojis to distinguish sources.

**Security Hardening** — DigitalOcean cloud firewall restricts the Wazuh server to home IP only for SSH, HTTPS, and API access. Port 1514 and 1515 restricted to honeypot droplet and home IP only. Fail2ban provides a second layer of SSH protection.

**MITRE ATT&CK Mapping** — All alerts automatically mapped to MITRE ATT&CK framework by Wazuh. Active techniques observed include T1110.001 (Password Guessing), T1021.004 (SSH), T1565.001 (Stored Data Manipulation), and T1078 (Valid Accounts).

---

## Environment Architecture

```
[Home Network — 192.168.40.x]
        |
        ├── PATPC (Windows 11) — Wazuh Agent 002
        ├── PatHP55 (Windows 11) — Wazuh Agent 004
        └── eljefe (Kali Linux) — Wazuh Agent 003 / Offensive Testing
                |
                | (port 1514 over internet)
                |
[DigitalOcean — New York]
        |
        ├── WazuhCMDC (Ubuntu 22.04) — Wazuh SIEM Server
        |       IP: 142.93.251.205
        |       Domain: wazuhseclab.xyz
        |       Services: Wazuh Manager, Indexer, Dashboard, Filebeat
        |       Firewall: DO cloud firewall (home IP only)
        |
        └── ubuntu-server-po (Ubuntu 24.04) — Honeypot
                IP: 159.89.53.117
                Services: OpenCanary (12 fake services), Wazuh Agent 001, Fail2ban
                Firewall: Wide open (intentional — attracts real attackers)
```

---

## Discord Alert Channels

| Channel | Alert Type | Trigger |
|---|---|---|
| #commandline-alerts | General security alerts | Rule level 5 and above |
| #high-wazuh-alerts | High severity alerts | Rule level 10 and above |
| #file-monitoring | File integrity events | Rules 550 through 558 |
| #abuseipdb-alerts | IP threat intelligence | SSH brute force rules 5710, 5712, 5762, 5763 |
| #cyber-news | OpenCanary honeypot hits | Rules 87103 through 87106 |
| #daily-threat-report | Daily AI summary | Cron job at 9 AM EST |
| #blocked-ips | Fail2ban ban/unban events | Honeypot and Wazuh server |
| #rules-and-commands | Reference channel | Manual |

---

## Tools and Technologies

- **Wazuh 4.14.5** — SIEM / XDR platform
- **OpenCanary** — Honeypot framework
- **Kali Linux** — Penetration testing OS
- **DigitalOcean** — Cloud infrastructure
- **AbuseIPDB** — IP threat intelligence API
- **Anthropic Claude API** — AI-generated daily threat reports
- **Discord** — Real-time alert notifications
- **Fail2ban** — Automated SSH brute force blocking
- **Certbot / Let's Encrypt** — Free SSL certificate for dashboard
- **Python** — Custom integration scripts
- **UFW** — Linux firewall management
- **Ubuntu Server** — Wazuh manager and honeypot host OS

---

## Real Attack Data

The honeypot receives thousands of automated attack attempts daily from internet-facing bots and scanners. Over 4,700 SSH attempts were logged in a single overnight period. Common attack patterns include SSH brute force with credential wordlists targeting usernames like root, admin, ubuntu, linux, deploy, and pi (indicating Raspberry Pi targeting).

All attacking IPs are automatically enriched with AbuseIPDB threat intelligence and included in the daily AI threat report.

---

## About

Built and maintained by [@projectPat003](https://github.com/projectPat003)

Focus areas: Threat detection, SIEM engineering, honeypot operations, cloud security, threat intelligence automation, Discord alerting pipelines

Georgia Tech MS in Cybersecurity — Starting September 2026
