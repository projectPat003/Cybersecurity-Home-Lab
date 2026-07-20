# Samples

Redacted threat intelligence report samples produced as part of this home lab project. Each report follows the standardized five-section format documented in the parent README and is written in plain language suitable for both technical and non-technical audiences.

---

## Reports

| File | Threat | Type | Severity |
|---|---|---|---|
| remcos-rat-phishing.md | Remcos RAT Phishing Campaign | Malware / Phishing | High |
| bluehammer-windows-zerodday.md | BlueHammer Windows Zero-Day | CVE / LPE | Critical |
| azure-mcp-missing-auth.md | Azure MCP Server Missing Authentication | CVE | High |
| redhat-libcap-toctou.md | Red Hat libcap TOCTOU Race Condition | CVE | Medium |
| iranian-ics-attacks.md | Iranian-Affiliated ICS Attacks (CISA Alert) | APT / ICS | Critical |

---

## Format Reference

Each report contains these five sections:

1. **Overview** — Threat summary, CVSS score, affected versions, and threat actor context
2. **How the Attack Works** — Technical breakdown of the exploit mechanism or attack chain
3. **Impact** — CIA triad mapping and real-world consequences
4. **Detection** — IOCs, SIEM detection opportunities, and recommended Wazuh rules
5. **Recommended Actions** — Patches, mitigations, and hardening steps

> **NOTE:** All reports are redacted where applicable. No sensitive organizational data is included.
