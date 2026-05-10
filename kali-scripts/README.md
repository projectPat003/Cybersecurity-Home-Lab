
# Kali Linux Scripts

A collection of scripts and tools used during offensive security testing and home lab experimentation on Kali Linux. Built to support reconnaissance, enumeration, and detection validation against controlled environments.

---

## Contents

| Script | Description |
|---|---|
| `port_scan.sh` | Automated Nmap wrapper for quick host enumeration |
| `banner_grab.sh` | Service banner grabbing across a target subnet |
| `log_monitor.sh` | Monitors system logs in real time for anomalous activity |
| `ssh_bruteforce_test.sh` | Tests SSH login resilience against a local honeypot (authorized use only) |
| `net_recon.sh` | Basic network reconnaissance — ARP scan, traceroute, and OS detection |

---

## Usage

```bash
chmod +x script_name.sh
./script_name.sh
```

---

## Environment

- **OS:** Kali Linux (Acer Chromebook R11 — MrChromebox UEFI)
- **Purpose:** Offensive testing against home lab endpoints only
- **SIEM Validation:** Attacks logged and verified via Wazuh

---

## Disclaimer

All scripts are used exclusively in a controlled home lab environment against systems I own. Never use offensive tools against systems without explicit authorization.

---

## Author

[@projectPat003](https://github.com/projectPat003)
