# Cybersecurity Home Lab — projectPat003

A hands-on cybersecurity home lab environment built for threat detection, SIEM operations, honeypot deployment, and offensive security testing. This repository documents configurations, custom rules, scripts, and threat intelligence templates developed through active experimentation.

---

## Lab Overview

| Component | Details |
|---|---|
| SIEM Platform | Wazuh (self-hosted on Ubuntu VM) |
| Hypervisor | VirtualBox (Windows host) |
| Monitored Endpoints | Windows (PATPC, PatHP55), Linux (Kali) |
| Honeypot | OpenCanary |
| Offensive Testing | Kali Linux |
| Network | 192.168.40.x subnet, bridged adapter |

---

## Repository Structure

```
cybersecurity-home-lab/
├── README.md                        ← You are here
├── wazuh-siem/
│   ├── README.md                    ← Setup and architecture notes
│   ├── ossec.conf                   ← Manager configuration
│   └── agent-configs/               ← Per-endpoint agent configs
├── opencanary-honeypot/
│   ├── README.md                    ← Integration and deployment notes
│   ├── opencanary.conf              ← Honeypot config
│   ├── decoders/                    ← Custom Wazuh decoders
│   └── rules/                       ← Custom alerting rules
├── kali-scripts/
│   ├── README.md
│   └── scripts/                     ← Offensive testing scripts
└── threat-intel-reports/
    ├── README.md
    └── samples/                     ← Redacted threat intel report samples
```

---

## Key Capabilities Demonstrated

- **SIEM Deployment** — End-to-end Wazuh installation, manager/agent architecture, UFW firewall rules, and network troubleshooting across a multi-OS environment
- **Honeypot Integration** — OpenCanary deployed as a decoy node; alerts forwarded to Wazuh via custom decoders and rules
- **Active Response** — Automated IP blocking triggered by honeypot interaction events
- **Offensive Testing** — Kali Linux used to simulate attacks and validate detection coverage
- **Threat Intelligence** — Structured reporting using a five-section format aligned with industry standards (Overview, Attack Mechanics, Impact, Detection, Recommended Actions)

---

## Environment Architecture

```
[Windows Host — VirtualBox]
        |
        ├── Ubuntu VM ("iran") — Wazuh Manager
        |       IP: 192.168.40.x
        |       User: eljefe
        |
        ├── PATPC (Windows) — Wazuh Agent
        ├── PatHP55 (Windows) — Wazuh Agent
        └── Kali Linux (Chromebook R11) — Wazuh Agent / Offensive Testing
```

---

## Tools & Technologies

- **Wazuh** — SIEM / XDR platform
- **OpenCanary** — Honeypot framework
- **Kali Linux** — Penetration testing OS
- **VirtualBox** — Hypervisor
- **UFW** — Linux firewall management
- **Ubuntu Server** — Wazuh manager host

---

## About

Built and maintained by [@projectPat003](https://github.com/projectPat003)  
Focus areas: Threat detection, SIEM engineering, honeypot operations, threat intelligence reporting
