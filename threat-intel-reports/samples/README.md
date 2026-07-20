This section holds old threat reports that have been printed in preivous days;
Ex.

🛡️ Daily Threat Intelligence Report — June 30, 2026
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🤖 AI Executive Summary
24-Hour Threat Intelligence Brief – Executive Summary
Over the past 24 hours, our two monitored agents detected 1,116 alerts dominated by 826 brute force attempts from 97 unique IPs, with India (IN) as the primary source. Five high-threat IPs (abuse score 75+) were identified conducting password guessing attacks primarily against the "admin" account, representing a coordinated credential compromise risk. Four file integrity events warrant investigation to determine if any breach attempts succeeded. Immediate action required: Review authentication logs for successful logins from flagged IPs, enforce account lockout policies on high-value accounts like "admin," and consider geo-blocking or rate-limiting from IN if operationally feasible.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📊 Alert Volume — Last 24 Hours
Total Alerts: 1,116
🔴 Critical (15+): 0
🟠 High (12-14): 0
🔵 Medium (7-11): 4
⚪ Low (0-6): 1112
Unique Attacking IPs: 97
Busiest Hour: 21:00 UTC (128 alerts)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🖥️ Alerts by Agent
• ubuntu-server-po: 1,045
• WazuhCMDC: 71

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔴 Top Attacking IPs
• 171.61.22.56 — 🇮🇳 IN | Score: 100/100 | Hits: 40 | 🔴 HIGH
  ISP: Bharti Airtel Ltd, 1 Malviya Nagar Bhopal Madhya Pradesh 462003 | AbuseIPDB Reports: 103
• 186.147.162.215 — 🇨🇴 CO | Score: 100/100 | Hits: 40 | 🔴 HIGH
  ISP: Telmex Colombia S.A. | AbuseIPDB Reports: 1918
• 45.148.10.151 — 🇳🇱 NL | Score: 100/100 | Hits: 34 | 🔴 HIGH
  ISP: TECHOFF SRV LIMITED | AbuseIPDB Reports: 207149
• 103.97.101.25 — 🇮🇩 ID | Score: 100/100 | Hits: 32 | 🔴 HIGH
  ISP: Dinas Kominfo Kota Pekalongan | AbuseIPDB Reports: 2943
• 45.227.254.170 — 🇱🇹 LT | Score: 100/100 | Hits: 32 | 🔴 HIGH
ISP: XWIN UNIVERSAL LTD | AbuseIPDB Reports: 214811

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔑 Top Usernames Attempted
• admin — 118 attempts
• ubuntu — 73 attempts
• root — 42 attempts
• test — 35 attempts
• user — 29 attempts
• teamspeak — 24 attempts
• oracle — 20 attempts
• ali — 14 attempts
• testuser — 13 attempts
• minecraft — 12 attempts
• deployer — 12 attempts
• gitlab-runner — 12 attempts
• odoo — 12 attempts
• ftpuser — 11 attempts
• devops — 9 attempts

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚔️ MITRE ATT&CK Techniques
• Password Guessing — 826 hits
• SSH — 826 hits
• Valid Accounts — 25 hits
• Sudo and Sudo Caching — 21 hits
• Remote Services — 2 hits
• Brute Force — 1 hits
• File Deletion — 1 hits
• Data Destruction — 1 hits

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📁 File Integrity Events
Total FIM Alerts: 4
Brute Force Attempts: 826

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔎 Top Triggered Rules
• Rule 5710 — 826 times
• Rule 5762 — 204 times
• Rule 5501 — 23 times
• Rule 5502 — 22 times
• Rule 5402 — 21 times
• Rule 5740 — 6 times
• Rule 11 — 4 times
• Rule 554 — 3 times
• Rule 5715 — 2 times
• Rule 533 — 2 times

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Active Agents: 2
• WazuhCMDC (127.0.0.1)
• ubuntu-server-po (159.89.53.117)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
