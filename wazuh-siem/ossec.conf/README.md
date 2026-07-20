# ossec.conf

The main Wazuh manager configuration file located at `/var/ossec/etc/ossec.conf` on the Wazuh server. This file controls everything the manager does — log collection, file integrity monitoring, active response, integrations, and cluster settings.

---

## Important Rules

The entire config must live inside a single `<ossec_config>` block. Having two separate blocks causes the manager to fail on startup with an XML parse error. Always validate the file before restarting:

```bash
sudo xmllint --noout /var/ossec/etc/ossec.conf
```

---

## Key Sections

**Global** — Sets alert log format, email notification settings, and agent disconnection timers.

**Alerts** — Sets the minimum log alert level (currently 3) and email alert level (currently 12).

**Remote** — Configures the agent communication port (1514 TCP).

**Rootcheck** — Policy monitoring that checks for rootkits, trojans, and suspicious processes every 12 hours.

**Syscollector** — Collects hardware, OS, network, package, port, process, user, and service inventory from all agents every hour.

**Vulnerability Detection** — Scans agents for known CVEs and updates the vulnerability feed every 60 minutes.

**Syscheck** — File integrity monitoring on the manager itself covering /etc, /usr/bin, /usr/sbin, /bin, /sbin, and /boot. File limit raised to 200,000 entries.

**Localfile** — Defines log sources including journald, dpkg.log, active-responses.log, netstat output, and last login output.

**Integrations** — Five integration blocks routing alerts to Discord and AbuseIPDB based on rule level and rule ID.

**Auth** — Agent enrollment configuration on port 1515.

**Cluster** — Cluster mode is disabled (single node deployment).

---

## Integration Blocks

| Integration | Channel | Trigger |
|---|---|---|
| custom-discord | #high-wazuh-alerts | Rule level 10 and above |
| custom-discord | #commandline-alerts | Rule level 5 and above |
| custom-discord | #file-monitoring | Rules 550 through 558 |
| custom-discord | #cyber-news | Rules 87103 through 87106 (OpenCanary) |
| custom-virustotal-ip | #abuseipdb-alerts | Rules 5710, 5712, 5762, 5763 (SSH brute force) |

---

## Useful Commands

```bash
# Validate config before restarting
sudo xmllint --noout /var/ossec/etc/ossec.conf

# Restart the manager
sudo systemctl restart wazuh-manager

# Restart all Wazuh daemons
sudo /var/ossec/bin/wazuh-control restart

# Check all daemon status
sudo /var/ossec/bin/wazuh-control status
```
