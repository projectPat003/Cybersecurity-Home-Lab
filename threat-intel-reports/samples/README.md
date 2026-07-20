This section holds old threat reports that have been printed in preivous days;
Ex.

# 📋 Daily Threat Intelligence Reports — Archive

This section holds historical daily threat intelligence reports generated automatically by the AI pipeline running on the Wazuh SIEM. Reports are posted to Discord every morning at 9 AM EST and archived here for reference.

---

## 🛡️ June 30, 2026

### 🤖 AI Executive Summary

Over the past 24 hours, two monitored agents detected 1,116 alerts dominated by 826 brute force attempts from 97 unique IPs, with India as the primary source country. Five high-threat IPs (abuse score 75+) were identified conducting password guessing attacks primarily against the admin account, representing a coordinated credential compromise risk. Four file integrity events warrant investigation to determine if any breach attempts succeeded.

**Immediate action required:** Review authentication logs for successful logins from flagged IPs, enforce account lockout policies on high-value accounts like admin, and consider geo-blocking or rate-limiting from India if operationally feasible.

---

### 📊 Alert Volume

| Metric | Value |
|---|---|
| Total Alerts | 1,116 |
| 🔴 Critical (Level 15+) | 0 |
| 🟠 High (Level 12-14) | 0 |
| 🔵 Medium (Level 7-11) | 4 |
| ⚪ Low (Level 0-6) | 1,112 |
| Unique Attacking IPs | 97 |
| Busiest Hour | 21:00 UTC (128 alerts) |

---

### 🖥️ Alerts by Agent

| Agent | Alert Count |
|---|---|
| ubuntu-server-po | 1,045 |
| WazuhCMDC | 71 |

---

### 🔴 Top Attacking IPs (the flags did not render, but there are flags that load beside the name of the country)

| IP | Country | Score | Hits | ISP | AbuseIPDB Reports |
|---|---|---|---|---|---|
| 171.61.22.56 | 🇮🇳 India | 🔴 100/100 | 40 | Bharti Airtel Ltd | 103 |
| 186.147.162.215 | 🇨🇴 Colombia | 🔴 100/100 | 40 | Telmex Colombia S.A. | 1,918 |
| 45.148.10.151 | 🇳🇱 Netherlands | 🔴 100/100 | 34 | TECHOFF SRV LIMITED | 207,149 |
| 103.97.101.25 | 🇮🇩 Indonesia | 🔴 100/100 | 32 | Dinas Kominfo Kota Pekalongan | 2,943 |
| 45.227.254.170 | 🇱🇹 Lithuania | 🔴 100/100 | 32 | XWIN UNIVERSAL LTD | 214,811 |

---

### 🔑 Top Usernames Attempted

| Username | Attempts |
|---|---|
| admin | 118 |
| ubuntu | 73 |
| root | 42 |
| test | 35 |
| user | 29 |
| teamspeak | 24 |
| oracle | 20 |
| ali | 14 |
| testuser | 13 |
| minecraft | 12 |
| deployer | 12 |
| gitlab-runner | 12 |
| odoo | 12 |
| ftpuser | 11 |
| devops | 9 |

---

### ⚔️ MITRE ATT&CK Techniques

| Technique | Hits |
|---|---|
| Password Guessing (T1110.001) | 826 |
| SSH (T1021.004) | 826 |
| Valid Accounts (T1078) | 25 |
| Sudo and Sudo Caching (T1548.003) | 21 |
| Remote Services (T1021) | 2 |
| Brute Force (T1110) | 1 |
| File Deletion (T1070.004) | 1 |
| Data Destruction (T1485) | 1 |

---

### 📁 File Integrity Events

| Metric | Value |
|---|---|
| Total FIM Alerts | 4 |
| Brute Force Attempts | 826 |

---

### 🔎 Top Triggered Rules

| Rule ID | Trigger Count | Description |
|---|---|---|
| 5710 | 826 | SSH login attempt with non-existent user |
| 5762 | 204 | SSH brute force — multiple auth failures |
| 5501 | 23 | Login session opened |
| 5502 | 22 | Login session closed |
| 5402 | 21 | Sudo: user NOT in sudoers |
| 5740 | 6 | SSH connection attempt |
| 11 | 4 | Syslog unknown message |
| 554 | 3 | File deleted |
| 5715 | 2 | SSH login failed |
| 533 | 2 | Process exited |

---

### ✅ Active Agents

| Agent | IP |
|---|---|
| WazuhCMDC | 127.0.0.1 |
| ubuntu-server-po | 159.89.53.117 |
