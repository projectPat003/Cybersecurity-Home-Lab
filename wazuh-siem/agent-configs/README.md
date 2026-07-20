# Agent Configs

Centralized Wazuh agent configuration files pushed to endpoint groups via the Wazuh manager. These configs are stored in `/var/ossec/etc/shared/<group-name>/agent.conf` on the Wazuh server and automatically distributed to all agents in the corresponding group.

Using agent groups means FIM paths, scan frequencies, and file limits can be managed from one place on the manager without logging into each endpoint individually.

---

## Groups

| Group | Members | Config File |
|---|---|---|
| windows-agents | PATPC (002), PatHP55 (004) | windows-agents.conf |
| kali-agent | eljefe (003) | kali-agent.conf |

---

## windows-agents.conf

Applied to PATPC and PatHP55. Monitors high-value Windows directories in realtime and performs scheduled scans of system directories every 5 hours.

**Realtime monitoring:**

| Path | Reason |
|---|---|
| C:\Users\patrj\Downloads | Where malware commonly lands |
| C:\Users\patrj\Desktop | Where users commonly run executables |
| C:\Users\patrj\Documents | User document storage |

**Scheduled monitoring (every 5 hours):**

| Path | Reason |
|---|---|
| C:\Windows\System32 | Core Windows system files |
| C:\Program Files | Installed software |
| C:\Program Files (x86) | 32-bit installed software |
| Startup folder | Common malware persistence location |
| HKLM\Software\Microsoft\Windows\CurrentVersion\Run | Registry run key persistence |
| HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce | Registry run once persistence |
| HKLM\System\CurrentControlSet\Services | Windows services persistence |

> **NOTE:** File limit raised to 200,000 entries. PATPC was hitting the default 100,000 limit due to the large number of Windows system files being monitored.

---

## kali-agent.conf

Applied to eljefe. Monitors the root home directory and user home in realtime and performs scheduled scans of key system directories every 5 hours.

**Realtime monitoring:**

| Path | Reason |
|---|---|
| /root | Root user home directory — any changes here are significant |
| /home/eljefe | Primary user home directory |

**Scheduled monitoring (every 5 hours):**

| Path | Reason |
|---|---|
| /etc | System configuration files |
| /usr/bin | System binaries — detect if tools are added or modified |
| /tmp | Common staging area for exploits and malware |

---

## How Agent Groups Work

1. The manager reads the `agent.conf` file from `/var/ossec/etc/shared/<group-name>/`
2. When an agent checks in it downloads the shared config and merges it with its local config
3. Changes to the group config on the manager are automatically picked up by agents on their next check-in cycle
4. No need to SSH into individual endpoints to update FIM paths

**Useful commands:**

```bash
# List all agents in a group
sudo /var/ossec/bin/agent_groups -l -g windows-agents

# Check which groups an agent belongs to
sudo /var/ossec/bin/agent_groups -s -i 002

# Force a FIM scan on a specific agent
sudo /var/ossec/bin/agent_control -r -u 002

# Verify agent is active and last FIM scan time
sudo /var/ossec/bin/agent_control -i 002
```

> **NOTE:** After updating an agent.conf file always restart the Wazuh manager so agents receive the updated config on their next check-in.

```bash
sudo systemctl restart wazuh-manager
```
