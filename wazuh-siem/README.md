
# Wazuh SIEM — Setup & Configuration

This section documents the deployment and configuration of a self-hosted Wazuh SIEM environment, including manager setup, multi-agent enrollment, network troubleshooting, and firewall configuration.

---

## Architecture

| Role | Host | OS | IP |
|---|---|---|---|
| Wazuh Manager | "iran" VM | Ubuntu (VirtualBox) | 192.168.40.x |
| Agent | PATPC | Windows | 192.168.40.x |
| Agent | PatHP55 | Windows | 192.168.40.x |
| Agent | Kali Machine | Kali Linux | 192.168.40.x |

---

## Manager Setup

- Deployed Wazuh manager on an Ubuntu VM running inside VirtualBox on a Windows host
- Configured VirtualBox bridged adapter to bind the VM to the correct physical NIC, resolving subnet misconfiguration between the VM's internal IP and the actual 192.168.40.x home network
- Managed inbound firewall rules via UFW to allow Wazuh agent communication on required ports

### Key UFW Rules

```bash
sudo ufw allow 1514/tcp   # Agent communication
sudo ufw allow 1514/udp
sudo ufw allow 1515/tcp   # Agent enrollment
sudo ufw allow 55000/tcp  # Wazuh API
sudo ufw enable
```

---

## Agent Enrollment

Agents enrolled across Windows and Linux endpoints using the Wazuh agent installer with the manager IP specified at install time.

**Windows (PowerShell):**
```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.x.x.msi -OutFile wazuh-agent.msi
msiexec /i wazuh-agent.msi WAZUH_MANAGER="192.168.40.x" /q
NET START WazuhSvc
```

**Linux:**
```bash
WAZUH_MANAGER="192.168.40.x" apt-get install wazuh-agent
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

---

## Troubleshooting Notes

- **Subnet mismatch** — VirtualBox defaulted to a NAT adapter IP instead of the bridged 192.168.40.x network; resolved by explicitly selecting the correct physical NIC in the bridged adapter settings
- **Agent connectivity** — Confirmed UFW was not blocking ports 1514/1515 after initial enrollment failures
- **Agent status verification** — Used `sudo /var/ossec/bin/agent_control -l` to list connected agents and confirm enrollment

---

## Log Collection

Wazuh configured to collect:
- Windows Event Logs (Security, System, Application)
- Linux syslog and auth.log
- Custom log paths for OpenCanary honeypot output

---

## References

- [Wazuh Documentation](https://documentation.wazuh.com)
- [Wazuh Agent Enrollment Guide](https://documentation.wazuh.com/current/user-manual/agent-enrollment/index.html)
