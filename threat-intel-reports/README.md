
# Threat Intelligence Reports

Structured threat intelligence reports covering real-world CVEs, APT campaigns, and critical infrastructure threats. Each report follows a standardized five-section format used in professional threat intelligence workflows.

---

## Report Format

| Section | Description |
|---|---|
| **Overview / Affected Software** | Vulnerability summary, CVSS score, affected versions |
| **How the Attack Works** | Technical breakdown of the exploit mechanism |
| **Impact** | CIA triad mapping and real-world consequences |
| **Detection & Testing** | IOCs, SIEM detection opportunities, Wazuh rules |
| **Recommended Actions** | Patches, mitigations, and hardening steps |

---

## Reports

| Report | Type | Severity | Summary |
|---|---|---|---|
| **Remcos RAT Phishing Campaign** | Malware / Phishing | High | Phishing campaign delivering Remcos RAT via malicious attachments |
| **BlueHammer Windows Zero-Day** | CVE / LPE | Critical | Local privilege escalation zero-day affecting Windows systems |
| **Azure MCP Server Missing Authentication** | CVE | High | Missing authentication flaw in Azure MCP Server allowing unauthorized access |
| **Red Hat libcap TOCTOU Race Condition** | CVE | Medium | Time-of-check to time-of-use race condition in Red Hat libcap |
| **Iranian-Affiliated ICS Attacks (CISA Alert)** | APT / ICS | Critical | CISA alert on Iranian-affiliated threat actors targeting US critical infrastructure |

---

## Tools & Frameworks Referenced

- **MITRE ATT&CK** — TTP mapping
- **CVSS v3.1** — Severity scoring
- **Wazuh** — Detection rule development
- **CISA Advisories** — Threat intelligence sourcing

---

## Author

[@projectPat003](https://github.com/projectPat003)
