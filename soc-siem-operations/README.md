# SOC playbooks

Investigation playbooks used for triage, containment, and response across common SOC alert categories. Each playbook follows the same structure so analysts can move between scenarios without relearning format.

Part of my [security portfolio](https://www.linkedin.com/in/lua-cybersecurity-prof/) · [LinkedIn Services](https://www.linkedin.com/services/page/1943b0320a0418661a )

## Playbook structure

Every playbook in this repo follows:

1. **Severity** — default classification and what escalates it
2. **MITRE ATT&CK mapping** — tactics/techniques involved
3. **Detection logic** — what triggers the alert, common data sources
4. **Investigation steps** — the actual triage sequence, in order
5. **Containment** — immediate actions to stop spread/impact
6. **Eradication** — removing the root cause
7. **Recovery** — returning to normal operations safely
8. **Lessons learned** — tuning feedback loop

## Index

| Playbook | Primary tactic |
|---|---|
| [Brute Force Investigation](./Brute%20Force%20Investigation.md) | Credential Access |
| [Phishing Investigation](./Phishing%20Investigation.md) | Initial Access |
| [Ransomware Investigation](./Ransomware%20Investigation.md) | Impact |
| [Impossible Travel](./Impossible%20Travel.md) | Initial Access / Credential Access |
| [Suspicious PowerShell](./Suspicious%20PowerShell.md) | Execution |
| [Credential Dumping](./Credential%20Dumping.md) | Credential Access |
| [Web Shell Detection](./Web%20Shell%20Detection.md) | Persistence |
| [Insider Threat](./Insider%20Threat.md) | Collection / Exfiltration |

## Note

All detection logic and examples are written as generalized, platform-agnostic methodology (referencing Splunk/Sentinel query patterns conceptually) built from operating SIEM/EDR platforms in production. No client data, real IOCs, or proprietary configurations are included.
